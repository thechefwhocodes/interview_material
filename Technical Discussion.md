# Phia In-House Search Platform

_Staff Software Engineer, AI/ML — Technical Deep Dive prep_

---

## Context & Problem

- Before I joined, Phia's entire search experience was outsourced to the Google Shopping API
- This meant zero control over personalization, discovery experiences for customer
- Premium retail partners had strict requirements on how their products surfaced on our platform
- Stakeholders: CEO, CTO, business partnerships team, end users
- Goal
  - Replace Google Shopping API with an in-house search platform
  - Buidling and scaling to 500M products
  - At parity or better latency/relevance
- First ML Engineer and leadership wasn't sure if we had resources to build it.

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
  - 1000 Human-labeled products as ground truth
  - **Additional Points**
    - Contained both +ve and -ve examples
    - Wrote explicit rubric with edge cases (e.g., "navy vs. black when image is ambiguous")
    - Revised rubric until labelers agreed about 95% of the time
- LLM-as-a-Judge
  - Used a different model family (Claude Sonnet 4) than the production model to judge outputs
  - Calibrated the judge on golden dataset to predict the metadata and measure three metrics (Groundedness, Correctness, Completeness)
  - In production: judge scored on 10% of the traffic, sampled across category, seller and price.
  - **Additional Points**
    - Judge vs human agreement around 90%
    - Groundedness: strict threshold, ~90%, since a fabricated attribute misleads customers
    - Correctness: ~90% threshold, tied back to business tolerance (calibrated against return/complaint-rate data)
    - Completeness: looser threshold, ~75%, since a missing field is a minor annoyance, not active harm

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
- Fine-tuned with LoRA adapters, fp8, single GPU
- Evaluated against human golden set using determinitic checks
- Result: fine-tuned model outperformed GPT-4o-mini
- **Addtional Points**
  - Consensus check
    - kept examples where 2/3 models agreed
    - disagreements and 10% of silver dataset audited by humans
  - Validation step:
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
      COLOR: ,
      ...
      }
      <|im_end|>

#### Serving

- Deployed trained Qwen3-8B model on Modal's H100 with vLLM in fp8
- Batch job to infer metadata
- **Addtional Points**
  - Used prefix caching for system prompt
  - ~100 H100s using enterprise account
  - Back of the envelope calculation
    - $400/day @ $4/hour = 100 GPU-hours/day
    - 100 containers with a GPU each
      - 10M products in an hour using 100 GPUs = 100K products in an hour per GPU = 28 products/sec per GPU
      - 600 input and 150 output token per product = 28 \* (600 + 150) total token per GPU per sec
  - Why Modal
    - No dedicated infra needed
    - GPU only required during the batch window and shut down afterwards
    - Faster to ship
    - Hard to get GPU quota on GCP
  - If Modal goes down
    - Standard Qwen + LoRA (fine tuned weights) served with vLLM
    - Can be moved to any GPU provider

### Online Quality Control

- LLM-as-judge (Claude Sonnet 4) evaluated ~10% of traces stratified by category, seller and price.
- Human in the Loop when:
  - Model confidence is low
  - Judge and model disagree on metadata
  - Small % of agreement are also raised to human
- Every human correction feeds back into the golden dataset for evals
- **Additional Points**
  - Judge coverage was stratified
    - Covering all categories/sellers/price buckets, oversampling new sellers and new categories where the model has the least signal
  - Flagged products goes to a queue for human labeling

---

## Search & Ranking (inference-time)

- Hybrid search: BM25 (keyword) + embedding search (FashionCLIP, multi-modal text+image)
- Ranking
  - XGBoost model over both recall sets
  - Using seller/product historical performance, price bucket, etc. as features
  - CTR as label

---

## Results

- CTR 2x, add-to-watchlist 8x
- Latency 700ms → 500ms
- Enrichment cost $1200/day -> $400/day. Judge costed $2200/day

---

## Next Steps

- Quantize the embeddings to reduce cluster size and retrieval speed
- Ablation study to measure the performance of every change independently
- Deduping products based on their product ids
- Enforce fixed list of allowed values and enforce it in output schema to control model updates
- Reduce cost for LLM-as-a-judge
