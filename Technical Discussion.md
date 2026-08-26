# Phio In-House Search Platform
*Staff Software Engineer, AI/ML — Technical Deep Dive prep*

---

## Context & Problem

- Before I joined, Phia's entire search experience was outsourced to the Google Shopping API
- This meant zero control over personalization and discovery experiences for customer
- Premium retail partners had strict requirements on how their products surfaced on our platform
- Stakeholders: CEO, CTO, business partnerships team, end users
- *Goal*
    - Replace Google Shopping API with an in-house search platform
    - Scaling to 500M products
    - At parity or better latency/relevance

---

## Phase 1: Proof of Concept (5M products, single category)
To de-risk the bet, I started with 5M shoe-category products before committing to building the platform for 500M products

**Pipeline architecture**
I build the ETL pipeline for the POC using 5M products
- Orchestration: Airflow
- Compute: GCP Cloud Run jobs + PySpark
- Steps: download → normalize/clean → metadata enrichment (GPT-4o-mini) → embedding generation (FashionCLIP model) → ingest into OpenSearch

**Eval methodology for Metadata Enrichment**
- Built a golden evaluation dataset
    - Human-labeled (internal team + Mechanical Turk) as ground truth
    - Wrote explicit labeling rubric with edge cases (e.g., "navy vs. black when image is ambiguous")
    - Measured labeler's agreement (3 labels for each dataset)
    - Rewrote the rubric until agreement was consistently high (~95%)
- LLM-as-a-Judge
    - Used a **different** model family (Claude Sonnet 4) than the production model to judge outputs
    - Measured three metrics:
        - **Groundedness** — strict threshold, ~90%, since a hallucinated attribute can misleads customers
        - **Correctness** — ~90% threshold, tied back to business tolerance (calibrated against return/complaint-rate data)
        - **Completeness** — looser threshold, ~75%, since a missing field is a minor annoyance, not active harm

**Results**
- A/B tested: +24% product CTR, +28% add-to-watchlist
- Latency: 700ms → 500ms
- This became the business case for the next quarter's scale-up investment

---

## Phase 2: Scaling to 500M Products

**Engineering for scale**
- Delta processing: only process on new/changed products (~50M/day), not the full 500M catalog every run
- Checkpointed intermediate results to GCS between pipeline stages
    - A failure at any stage resumes from that stage, not from the start

**Cost-driven model decision**
- Back-of-envelope: GPT-4o-mini at full scale ≈ $1,200/day — reviewed with CFO, not viable long-term
- I proposed testing a smaller open-source model (Qwen3-8B) as a cheaper alternative
- Out-of-the-box performance was worse than GPT-4o-mini, so I drove fine-tuning
- Delegated hosting/serving infrastructure to our MLOps team 
— I owned the model and data side

**Fine-tuning data**
- ~5000 examples across 50 categories (~100/category), covering multiple sellers and subcategories
- Silver dataset generated using multiple (Sonnet 4, Gemini Flash 2.5, GPT 4o) models
- Consensus check
    - kept examples where 2/3 models agreed
    - disagreements routed to humans
    - Consensus matching was too hard (identifying whether models were even labeling the *same* product attributes)
    - Fallback to no consensus but with human-review on sampled data
- **Key validation step:** 
    - Silver data quality matters less in isolation 
    — What matters is validating the *final fine-tuned model's* output against the trusted human-labeled golden set. 
    - If the fine-tuned model scores well against real ground truth, noise in the silver data didn't propagate meaningfully
- Fine-tuned with LoRA adapters, fp8, single GPU
- Result: fine-tuned model matched and outperformed GPT-4o-mini on correctness, comparable on groundedness/completeness across multiple runs

**Inference-time quality control**
- LLM-as-judge (Claude Sonnet 4) evaluated % of traces against the production traffic
- Escalation to human review when: 
    - Model confidence is low
    - Judge and model disagree
- Judge coverage was **stratified** 10% sample
    - Covering all categories/sellers/price buckets, oversampling new sellers and new categories where the model has the least signal
    - Ran periodic 30% coverage judge passes (e.g., weekly) to validate the 10% sample's disagreement rate matches
- Flagged products goes to a queue for human labeling
- Every human correction feeds back into the golden dataset for evals
- User feedback loop on returns or support tickets also fed back to eval dataset

---

## Search & Ranking (inference-time)

- Hybrid search: BM25 (keyword) + embedding search (FashionCLIP, multi-modal text+image)
- Re-ranking
    - XGBoost model over both recall sets
    - Using seller/product historical performance, price bucket, etc. as features
    - CTR as label
    - The re-ranker was owned by a teammate, not me

---

## Results

- CTR 2x, add-to-watchlist 8x
- Latency 700ms → 500ms

## Next Steps

- Deduping
- Embedding dimension reduction

---

## Staff-Level Threads to Keep Consistent Under Follow-Up

1. **Thresholds are never arbitrary** — always tied back to a business cost signal (returns, complaints)
2. **Ground truth quality is validated, not assumed** — rubric + labeler agreement for humans, sample audits for LLM-generated dataset
3. **Silver data's accuracy matters less than the final model's validated performance** against the trusted golden set
