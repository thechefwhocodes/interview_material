# Phio In-House Search Platform

_Staff Software Engineer, AI/ML — Technical Deep Dive prep_

---

## Context & Problem

- Before I joined, Phia's entire search experience was outsourced to the Google Shopping API
- This meant zero control over personalization, discovery experiences for customer
- Premium retail partners had strict requirements on how their products surfaced on our platform
- Stakeholders: CEO, CTO, business partnerships team, end users
- _Goal_
  - Replace Google Shopping API with an in-house search platform
  - Scaling to 500M products
  - At parity or better latency/relevance
- First ML Engineer and leadership wan't sure if we could build it.

---

## Phase 1: Proof of Concept (5M products, single category)

To de-risk, I started with 5M shoe-category products before committing to building the platform for 500M products

### Pipeline architecture

- Orchestration: Airflow
- Compute: GCP Cloud Run jobs + PySpark
- Steps: download → normalize/clean → metadata enrichment (GPT-4o-mini) → embedding generation (FashionCLIP model) → ingest into OpenSearch for keyword and vector search

### Results

- A/B tested with 50-50% traffic for 2 weeks on shoe category only
- +24% product CTR, +28% add-to-watchlist
- Latency: 700ms → 500ms

## Eval methodology for Metadata Enrichment

Everything downstream now depended on the LLM's metadata being right. So before scaling, I needed a way to measure that.

- Built a golden evaluation dataset
  - 1000 Human-labeled products (internal team + Mechanical Turk) as ground truth
    - Contains both +ve and -ve examples
  - Wrote explicit rubric with edge cases (e.g., "navy vs. black when image is ambiguous")
  - Revised rublic until labelers agreed about 95% of the time
- LLM-as-a-Judge
  - Used a **different** model family (Claude Sonnet 4) than the production model to judge outputs
  - Calibrated the judge on golden dataset
  - Measured three metrics:
    - **Groundedness** — strict threshold, ~90%, since a fabricated attribute misleads customers
    - **Correctness** — ~90% threshold, tied back to business tolerance (calibrated against return/complaint-rate data)
    - **Completeness** — looser threshold, ~75%, since a missing field is a minor annoyance, not active harm
  - In production: judge scored on 10% of the traffic, sampled across category, seller and price.

---

## Phase 2: Scaling to 500M Products

Going 100x broke three things: daily processing, LLM cost, and online quality checks at scale.

### Daily Processing

- Instead of processing full 500M catalog every run, only process on new/changed products (~50M/day)
- Checkpointed intermediate results to GCS
- A failure at any stage resumes from that stage, not from the start

### Cost-driven model decision

- Back-of-envelope: GPT-4o-mini at full scale ≈ $1,200/day ($440K/year) — reviewed with CFO, not viable long-term
- I proposed testing a smaller open-source model (Qwen3-8B) as a cheaper alternative
- Out-of-the-box performance was worse than GPT-4o-mini, so I drove fine-tuning

#### Fine-tuning

- Created ~10,000 examples across 50 categories (~200/category), covering multiple sellers and subcategories
- Silver dataset generated using multiple (Sonnet 4, Gemini Flash 2.5, GPT 4o) models
- Consensus check
  - kept examples where 2/3 models agreed
  - disagreements routed to humans
- Fine-tuned with LoRA adapters, fp8, single GPU
- Evaluated against human golden set using LLM-as-a-Judge
- Result: fine-tuned model outperformed GPT-4o-mini on ground truth on correctness and at par for goundedness and completeness

#### Additional Info

- Key validation step:
  - Silver data quality matters less in isolation
    — What matters is validating the final fine-tuned model's output against the trusted human-labeled golden set.
  - If the fine-tuned model scores well against real ground truth, noise in the silver data didn't propagate meaningfully
- Each training example looked as below:
  - <|im_start|>system
    SYSTEM PROMPT
    <|im_end|>

  <|im_start|>user
  PRODUCT TITLE, DESCRIPTION, AVAILABLE METADATA
  <|im_end|>

  <|im_start|>assistant:
  {
  BRAND: ,
  CATEGORY: ,
  SKU: ,
  COLOR: ,
  ...
  }
  <|im_end|>

### Online Quality Control

- LLM-as-judge (Claude Sonnet 4) evaluated 10% of traces
- Human in the Loop when:
  - Model confidence is low
  - Judge and model disagree
- Judge coverage was **stratified**
  - Covering all categories/sellers/price buckets, oversampling new sellers and new categories where the model has the least signal
  - Ran judge periodically on 30% of data (e.g., weekly) to validate the 10% sample's disagreement rate matches
- Flagged products goes to a queue for human labeling
- Every human correction feeds back into the golden dataset for evals
- User feedback loop on returns or support tickets also fed back to eval dataset

---

## Search & Ranking (inference-time)

- Hybrid search: BM25 (keyword) + embedding search (FashionCLIP, multi-modal text+image)
- Ranking
  - XGBoost model over both recall sets
  - Using seller/product historical performance, price bucket, etc. as features
  - CTR as label
  - The ranker was owned by a teammate, not me

---

## Results

- CTR 2x, add-to-watchlist 8x
- Latency 700ms → 500ms

---

## Staff-Level Threads to Keep Consistent Under Follow-Up

1. **Thresholds are never arbitrary** — always tied back to a business cost signal (returns, complaints)
2. **Ground truth quality is validated, not assumed** — rubric + labeler agreement for humans, sample audits for LLM-generated dataset
3. **Silver data's accuracy matters less than the final model's validated performance** against the trusted golden set
