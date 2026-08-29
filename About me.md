# About me

Senior Machine Learning Engineer with 7+ years of experience designing, scaling, and deploying end-to-end ML systems—specializing in search, ranking, recommendation and LLM workflows across billions of items and massive user bases.

---

# Next Role:

I am passionate about the complete product lifecycle and ownership: translating complex problem into production-ready solutions that drives measurable customer and business impact. I am seeking a mission-driven team where I can solve high-stakes customer pain points at scale.

---

# Bezi:

I was hired as a Senior ML Engineer at Bezi to lead a specific R&D project around context and memory layer for the coding agent. Instead of spending months building the product, I proposed validating the hypothesis by running experiments and POCs.

While the initial experiments show the underlying technology work, it wasn't going to deliver on cost and latency reduction which business needed. I got positive feedback from the entire engineering team that identifying this early probably saved company several months of resources.

Around the same time CTO left and the company had to re-evaluated their roadmap. The project was cancelled and thus my role became redundant.

---

# Project Walk through

## Phia Search Platform

- Situation
  - Search powered by the Google Shopping API.
  - Zero control over customer experience.
  - Premium retail partners had strict constraints.
  - Stakeholders: CTO, CEO, Business Partnership teams, and end-users.
- Task
  - Core Objective: Replace Google Shopping API by building an in-house Search platform from scratch.
  - Key Challenges: Scale to 500M products with low latency, high relevance, and high availability.
  - Strategy: Run a POC on 5M products (shoes) before scaling to the full 500M catalog.
- Action
  - Built a distributed ETL pipeline
    - Data Download -> Cleaning -> Normalization -> Entity Extraction -> Data Ingestion
    - Using Airflow, Cloud Run jobs, Dataproc, and Open Search.
  - LLM and Vector Search
    - Utilized LLMs for entity extraction
    - Supervised Fine-tuning Qwen3 8B models
      - Reduced cost from $1200 to $400 which translated to $120K annualized saving
    - Built evals (Gold dataset and LLM-as-a-Judge)
    - Generated embeddings using Fashio Clip
    - Ingested documents into OpenSearch.
  - Built idempotent architectures to handle volume of data
  - Search & Ranking Optimization
    - Built a multimodal search experience using text and image embeddings
    - Built custom XGBoost ranker
    - Fine-tuned OpenSearch cluster performance
  - Testing & Validation
    - Applied stage-by-stage sanity checks,
    - Offline LLM/search evaluations
    - Internal dogfooding
    - Controlled A/B test.
- Result
  - Reduced Latency: Search response time dropped from 700ms to 500ms at massive scale.
  - Boosted Engagement: Delivered 2x increase in product clicks and an 8x increase in product favoriting.
- Reflection & Next Steps
  - Dimension Reduction: Reduce embedding dimensions to lower vector storage costs further.
    - This may impact the retrieval accuracy and have to judged against offline gates
    - The trade-off between reduced accuracy vs latency improvement have to judged
  - Deduplication: Implement automated product deduplication across incoming merchant feeds.
  - Personalization: Utilize user features to make the experience more personalized.

---

# Referral Notes

Aashish is a Machine Learning Engineer experienced in powering large-scale search, ranking, recommendation and LLM workflows for e-commerce engines. Spearheaded the real-time ranking infrastructure for eBay Live, engineering streaming and batch data pipelines (Kafka, Flink, Spark) and AI-driven interactive workflows that directly influenced $2B+ in GMV. Deeply passionate about solving low-latency discovery challenges in fast-paced, live shopping environments—connecting buyers and sellers in real-time through high-stakes, production-ready ML systems.

Aashish and I built a side project unifying voice and text orchestration for AI agents. Working with him showed me his incredible technical depth—he’s obsessively thorough with low-latency architecture and never settles for surface-level fixes. He lives and breathes this tech, and he’d be a massive asset for Hyde.

---
