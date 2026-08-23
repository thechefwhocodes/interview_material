# Amazon Leadership Principles

## Stories

---

### Phia Search Platform

**Situation**
- Search powered by the Google Shopping API.
- Zero control over customer experience.
- Premium retail partners had strict constraints.
- Stakeholders: CTO, CEO, Business Partnership teams, and end-users.

*Task*:
- Core Objective: Replace Google Shopping API by building an in-house Search platform from scratch.
- Key Challenges: Scale to 500M products with low latency, high relevance, and high availability.
- Strategy: Run a POC on 5M products (shoes) before scaling to the full 500M catalog.

**Action**
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

**Result**
  - Reduced Latency: Search response time dropped from 700ms to 500ms at massive scale.
  - Boosted Engagement: Delivered 2x increase in product clicks and an 8x increase in product favoriting.

---

### Open Search Partitioning
**Situation**
- At Phia, as we scaled the search platform, the CTO, wanted to partition our OpenSearch cluster into physical indexes per category and brand, to reduce query latency by shrinking the search space.

**Task**
- I disagreed, because managing hundreds of physical indexes as we added more brands and verticals would create significant infrastructure and DevOps overhead. 
- I proposed logical partitioning through metadata and pre-filtering ANN search
- But he was skeptical of my alternative, because in his understanding, since HNSW still navigates a similarly sized vector space, the latency wouldn't meaningfully improve either way. 
- So this wasn't a case of him dismissing an idea, he had a real technical reason to doubt it.

**Action**
- Instead of going back and forth on whose intuition was right, I proposed we settle it with a benchmark on real production data. 
- I ran both approaches head to head, physical indexes per brand versus logical indexes with pre-filtering.

**Result**
- The latency difference was negligible. 190ms versus 200ms. 
- The data let us choose the logical indexing approach with confidence, avoiding the engineering overhead of managing physical indexes at scale
- It aslo shifted how we approached similar disagreements afterward, benchmark first, debate second.

---

### Context Management and Memory Layer
**Situation**
- At Bezi, leadership, the CEO, CTO, and product lead, had a hypothesis, inspired by tools like Obsidian
- Better context management and memory layer could make coding agents more accurate and also cheaper and faster thus passing the savings to customers.
- They wanted to build a product around that idea.

**Task**
- I was hired specifically for this project.
- There was no clear direction on how to validate it.
- So I decide to validate their hypothesis, and share the finding with the leadership.

**Action**
- Instead of jumping straight to a monthslong build, I proposed running POCs first
- Researched context management and memory layer approaches
- Picked real projects with actual usage in collaboration with GTM
- And ran a controlled comparison, coding agent with context management and memory layer versus without.
- When leadership pushed back on your initial methodology, wanting broader coverage across projects of different complexity, I incorporated that and expanded the testing.

**Result**
- Context management and memory layer did improve accuracy, cutting the number of turns needed by thirty percent
- But it did not reduce latency or cost, because carrying that context across a session adds up in tokens.
- Even after retesting across more projects and complexity levels per their request, the result held. 
- I had to tell leadership their hypothesis, didn't hold up.
- They were disappointed, but the finding was solid and saved months of misdirected build effort.

---

### Shop the Look
**Situation**
- At eBay, my PM pitched an idea for letting users upload a photo and virtually try on different clothing items 
- We both felt it was strong enough to pitch as a proof of concept for leadership.

**Task**
- As the machine learning engineer, I owned the modeling side, but a standalone demo wouldn't be convincing.
- To convince the leadership, it needed to be live inside the actual app, which meant front end image upload and backend storage work well outside your role.

**Action**
- I designed the end to end system, then pitched it directly to the iOS team's PM, two of their engineers and a designer
- I got them bought in to volunteer their time, and led that small cross functional group
- Two ML engineers and two iOS engineers and a designer, to build the whole flow in under a week.

**Result** 
- We shipped a working end to end experience inside the eBay app
- We tested with an initial set of users, and pitched it to leadership
- The got excited to put it on the next quarter's roadmap. 
- It's live today as Shop the Look
- We also filed a patent on it.

---

## Leadership Principles and Stories

- Customer Obsession: **Phia Search Platform**

- Ownership: **Phia Search Platform**

- Invent and Simplify: **Open Search Partitioning**

- Are Right, A Lot: **Open Search Partitioning**

- Learn and Be Curious: **Context Management and Memory Layer**

- Hire and Develop the Best: 

- Insist on High Standards: 

- Think Big: **Shop the Look**

- Bias for Action: **Shop the Look**

- Earn Trust: **Context Management and Memory Layer**

- Dive Deep: **Phia Search Platform**

- Have Backbone; Disagree & Commit: **Open Search Partitioning**

- Deliver Results: **Shop the Look**
