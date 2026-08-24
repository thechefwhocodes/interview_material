# Amazon Leadership Principles

## Stories

---

## Phia Search Platform

**Situation**
- At Phia, search experience was outsourced to the Google Shopping API.
- Zero control over customer's shopping experience including personalization and discovery.
- Premium partners complained their products surfaced next to products from marketplaces like eBay and Poshmark, which hurt their brand reputation.

**Task**
- Leadership, including the CTO, were skeptical about investing in an in-house search platform.
- Worried it would take months of engineering effort with no guarantee that it will work better than existing experience.

**Action**
- Instead of accepting the degraded experience, I proposed running a POC of in-house search experience which can be built quickly.
- I picked a single category, shoes, roughly 5M products instead of the entire 500M catalog.
- Built an end-to-end ETL pipeline that downloaded and normalized data.
- Used LLMs to extract missing product metadata.
- Utilized fashion-CLIP models to generate embeddings for the products
- Ingested all documents into an OpenSearch cluster to enable low-latency inference.
- Ran an A/B test to understand if in-house search system is better than Google Shopping API.

**Result**
- Latency dropped from 700ms to 500ms.
- Clicks improved 2x and Product-to-Watchlist improved 8x.
- This shifted leadership from keeping the degraded experience to committing to scale the in-house platform across all 500M products.

---

### Open Search Partitioning

**Situation**
- At Phia, as we scaled the search platform from 5M to 500M products
- CTO wanted to physically partition OpenSearch cluster per brand and category to reduce inference latency by shrinking the search space per query.

**Task**
- Disagreed, managing hundreds of physical indexes as we scale would create significant infrastructure and DevOps overhead.
- Proposed logical partitioning instead, using metadata tagging and pre-filtering ANN search.
- CTO was skeptical on real technical grounds: believed the ANN search would be traversing a similarly sized vector space, so the latency gains wouldn't be meaningful.

**Action**
- Proposed we run a head-to-head benchmark on real production data.
- Picked brands and categories with 100M products, so the test would reflect real production scale.
- Added metadata information to 100M products by running an ETL pipeline
- Built physical index and logical partition.
- Benchmarked queries against both and measured P90 and P99 latency using a benchmarking tool.

**Result**
- Benchmark showed the latency difference was negligible: 190ms for physical partitioning vs 200ms for logical partitioning.
- 10ms reduction wasn't worth the engineering and DevOps overhead of managing physical indexes at scale.
- Commited to logical partitioning approach.
- Set the framework to lead with data instead of priors and biases

---

### Open Source LLM

**Situation**: 
- We used GPT-4o mini for entity extraction task for product metadata enrichment. 
- To cut cost and latency, we tested swapping the model with a Qwen3 8B open-source model.

**Task**: 
- I was responsible for the swap and the roll out of the new model.

**Action**:
- I compared the new model against GPT-4o mini model on offline metrics.
- Had LLM-as-a-Judge metric monitoring live traffic.
- Under deadline pressure, I skipped the pre-launch shadow test and went straight to production.
- Caught a sharp error-rate spike within an hour
- Real products were getting mistagged.
- Rolled back to GPT-4o mini model immediately.
- Since bad metadata had already been written for ~1 hour, I re-ran that day's pipeline on original model to overwrite the incorrect tags.
- Set a hard rollout gate for future model swaps/updates: 
  - A new model can't go live unless it beats the baseline offline metrics
  - No launch without A/B test or Shawdow deployment.

**Result**: 
- Full data corrected same day
- ~1 hour of real impact contained
- The two-gate process is now standard for every model swap/update.

---

### Context Management and Memory Layer

**Situation**
- At Bezi, the leadership (including CEO, CTO, and product lead) had a hypothesis, inspired by tools like Obsidian.
- They envisioned that context management and memory layer could make coding agents more accurate. 
- Along with making them cheaper and faster. 
- They wanted to pass on these $ savings to the customers and differentiate themselves from competitors.

**Task**
- I was hired specifically for this project.
- There was no clear direction on how to build it.
- So I decide to validate their hypothesis and share the finding with the leadership.

**Action**
- Instead of jumping straight to a monthslong build, I proposed running POCs first.
- I researched context management and memory layer approaches across industry leaders like Cursor, Codex and Claude Code.
- I had to ran a controlled experiment, involing coding agent with context management and memory layer versus without it.
- I picked real projects and tasks with actual usage in collaboration with GTM.
- The leadership pushed back on my initial project list stating it wasn't exhastive enough.
- They wanted broader coverage across projects and tasks of different complexity.
- I initially resisted but eventually cooperated and expanded my experiment lists.

**Result**
- Context management and memory layer did improve accuracy, cutting the number of turns needed by 30%.
- But it did not reduce latency or cost, because carrying context forward in the session adds tokens back.
- I had to tell leadership their hypothesis didn't hold up.
- They were disappointed and skeptic of the number.
- Booked seperate sessions to walk them through the actual agent behaviour and why it didn't reflect in latency and cost reduction.
- Decided to pivot the strategy toward using context layer as workspace for architecture and intent feature instead of a measure to reduce cost

---

### Shop the Look

**Situation**
- At eBay, my PM pitched an idea for letting users upload a photo and virtually try on different clothing items.
- We both felt it was strong enough idea to pitch as a proof of concept for leadership.
- But instead of just pitching an idea, we wanted to present a demo.

**Task**
- As the machine learning engineer, I owned the modeling side.
- But a standalone demo wouldn't be convincing for leadership.
- To convince the leadership, it needed to be live inside the actual ebay app, which meant front end and backend work, which was well outside my expertise.

**Action**
- I designed the end to end system, then pitched it directly to the iOS team's PM, two of their engineers and a designer.
- I got them to volunteer their time, and led that small cross functional group of two ML engineers, two iOS engineers and a designer.
- Held daily sync, unblocked engineers on approach by talking to SMEs (Subject Matter Experts) and tested end-to-end experience rigously with mutiple uses.
- We build the whole flow in under a week.

**Result** 
- We shipped a working end to end experience inside the eBay app.
- We tested with an internal users, and pitched it to leadership.
- They got excited to put it on the next quarter's roadmap. 
- The project is live today as Shop the Look. And we also filed a patent on it.

---

### Feedback

**Situation**
- At eBay, I was leading a cross-functional team of 6 engineers, building the next generation recommendations platform.
- I used to gave weekly updates to a stakeholder group that included senior engineers, two directors, and a VP.

**Task**
- After one of these updates, my director gave me a direct feedback that my communication was too technical for the VP.
- I was spending too much time on implementation details and technical comparisons, and not enough on high level progress.

**Action**
- I initially pushed back on the feedback, stating technical discussions were necessary for senior engineers
- We created a different sync to discuss technical details with Senior Engineers
- I took that feedback and restructured my next updates.
- I shifted away from technical details to
  - Overall Architecture
  - Milestone progress against the roadmap
  - And testing status framed in a way the VP could track without needing engineering context.

**Result**
- We noticed visible change in the VP's engagement.
- He went from passively acknowledging updates to actively asking questions about the progress and how the team was doing.
- He was actually absorbing and engaging with the information rather than just nodding along.

---

## Leadership Principles and Stories

- Customer Obsession: **Phia Search Platform**

- Ownership: **Phia Search Platform**

- Invent and Simplify: **Open Search Partitioning**

- Are Right, A Lot: **Open Search Partitioning**

- Learn and Be Curious: **Context Management and Memory Layer**

- Hire and Develop the Best: **Feedback**

- Insist on High Standards: **Phia Search Platform**

- Think Big: **Shop the Look**

- Bias for Action: **Shop the Look**

- Earn Trust: **Context Management and Memory Layer**

- Dive Deep: **Phia Search Platform**

- Have Backbone; Disagree & Commit: **Open Search Partitioning**

- Deliver Results: **Shop the Look**
