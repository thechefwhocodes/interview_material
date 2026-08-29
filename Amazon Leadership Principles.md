# Amazon Leadership Principles

## Stories

---

## 1. Open Source LLM Rollout
**Best for:** Move fast, better care can't wait | Also works for: resilience, owning a mistake

**Situation**
- At Phia, GPT-4o-mini handled entity extraction for product metadata enrichment.
- To cut cost and latency, we tested swapping in an open-source Qwen3-8B model.

**Task**
- I owned the swap and rollout end-to-end.

**Action**
- Benchmarked the new model against GPT-4o-mini on offline metrics.
- Set up LLM-as-judge monitoring on live traffic to catch drift.
- Under deadline pressure, made the call to skip shadow deployment and go straight to production — in hindsight, the wrong call.
- Caught a sharp error-rate spike within an hour of launch via the judge monitoring.
- Diagnosed that real products were being mistagged with wrong attributes.
- Rolled back to GPT-4o-mini immediately rather than trying to patch forward.
- Re-ran that day's pipeline on the original model to overwrite the ~1 hour of incorrect tags.
- Wrote and enforced a hard two-gate rollout rule for every future model swap 
    - Must beat baseline on offline metrics
    - Must pass shadow deployment or an A/B test

**Result**
- Full data corrected same day, no lasting customer impact.
- ~1 hour of real exposure, fully contained.
- The two-gate process became the standard for every model swap on the team going forward.

---

## 2. OpenSearch Partitioning
**Best for:** Lead with data | Also works for: disagreement with a manager

**Situation**
- As we scaled Phia's search platform from 5M to 500M products
- CTO wanted to physically partition the OpenSearch cluster per brand/category to cut latency.

**Task**
- I disagreed — managing hundreds of physical indexes at that scale looked like a major infra/DevOps burden
- But the CTO was skeptical of my logical-partitioning alternative on real technical grounds

**Action**
- Instead of arguing from opinion, I proposed a head-to-head benchmark on real production data.
- Picked 100M products across multiple brands/categories so results would reflect true production scale.
- Ran an ETL pipeline to add the metadata tagging needed for logical partitioning.
- Built both a physical index and a logical partition.
- Benchmarked both approaches, measuring P90 and P99 latency with a OpenSearch benchmarking tool.
- Brought the CTO the raw numbers instead of re-pitching.

**Result**
- Physical partitioning: 190ms. Logical partitioning: 200ms 
— 10ms gap not worth the ongoing DevOps overhead of managing hundreds of physical indexes.
- We committed to logical partitioning.

---

## 3. Shop the Look
**Best for:** Walk through walls | Secondary fit: Embrace a service mindset

**Situation**
- At eBay, my PM pitched letting users upload a photo and virtually try on clothing items 
— We both felt it was strong enough to bring to leadership.

**Task**
- We knew a slide deck wouldn't be convincing. 
- A live demo inside the actual eBay app would win it for us
— But front-end and backend work was well outside my are expertize as the ML engineer.

**Action**
- Designed the end-to-end system myself
- Took it to the iOS team's PM, two of their engineers, and a designer — none of whom reported to me.
- Got all four to volunteer time on top of their existing workload.
- Led five-person cross-functional group — two ML engineers, two iOS engineers, one designer — with no formal authority over any of them.
- Ran daily syncs to keep a volunteer group moving on a tight, self-imposed timeline.
- Unblocked engineers stuck on approach by pulling in subject-matter experts rather than letting them stay stuck.
- Tested the end-to-end experience with multiple real users before the leadership pitch
- Built the entire working flow in under a week.

**Result**
- Shipped a working, live experience inside the eBay app and pitched it to leadership.
- Leadership greenlit it for the next quarter's roadmap.
- It's live today as "Shop the Look," and we filed a patent on it.

---

## 4. Context Management and Memory Layer
**Best for:** Continuously learn | Also works for: product judgment, a cross-functional effort that didn't go as planned

**Situation**
- At Bezi, leadership — CEO, CTO, and product lead — had a hypothesis, inspired by tools like Obsidian
- They envisioned a context-management and memory layer could make coding agents more accurate *and* cheaper/faster
- Letting them pass the savings to customers.

**Task**
- I was hired specifically for this
- I had to figure out how to validate the hypothesis before committing to a build.

**Action**
- Instead of jumping into a months-long build, I validated the hypothesis first with a controlled experiment.
- Researched how industry leaders — Cursor, Codex, Claude Code — approached context management, since this wasn't an area I had prior expertise in.
- Designed a controlled experiment comparing a coding agent with a context/memory layer against one without.
- Worked with GTM to pick real projects and tasks with actual usage.
- When leadership pushed back that my initial project list wasn't broad enough
  - I initially resisted
  - Then rethought it and expanded the test set to cover a wider range of task complexity.
- Ran the experiment and had to deliver a finding that only half-confirmed the hypothesis
  - Accuracy improved (30% fewer turns needed)
  - But cost and latency did not, since carrying context forward adds tokens back.
- Booked dedicated working sessions to walk leadership through actual agent behavior and the token math, rather than just handing over a number they were skeptical of.

**Result**
- Leadership accepted the data even though it wasn't the answer they wanted.
- We pivoted the product strategy: repositioned the memory layer as a workspace for architecture and intent, rather than a cost-saving feature.

---

## 5. Feedback
**Best for:** Receiving hard feedback

**Situation**
- At eBay, I was leading a cross-functional team of 6 engineers building the next-generation recommendations platform
- Giving weekly updates to a stakeholder group of senior engineers, two directors, and a VP.

**Task**
- After one update, my director told me that my updates were too technical for the VP
- Too much time on implementation details, not enough on high-level progress.

**Action**
- My first reaction was to push back 
— I felt the technical details were necessary for the senior engineers in the room.
- We decided to create a separate technical sync specifically for the senior engineers.
- I took the feedback and restructured the updates around three things only
  - Overall architecture
  - Milestone progress against the roadmap
  - Testing status — framed so the VP could track it without needing engineering context.

**Result**
- The VP visibly changed how he engaged 
— From passively acknowledging updates to actively asking questions about progress and how the team was doing.
- He was actually absorbing the updates, not just nodding along.

---

## 6. Phia Search Platform POC
**Best for:** Owning ambiguity / driving a project despite skepticism | Also covers: mentoring

**Situation**
- At Phia, the entire search experience was outsourced to the Google Shopping API 
— Zero control over personalization or discovery experience
- Premium partners were unhappy their products surfaced next to marketplace listings like eBay or Poshmark, hurting their brand.

**Task**
- Leadership, including the CTO, was skeptical of investing in an in-house platform 
— Worried it would take months of engineering effort with no guarantee it would beat the existing experience.

**Action**
- I proposed a scoped POC: one category (shoes), ~5M products instead of the full 500M catalog
- Chose OpenSearch for its native hybrid (BM25 + vector) search support
  - Also avoiding a separate vector database.
- Designed and built the end-to-end pipeline myself
  - Download and normalize data
  - Use LLMs to fill missing product metadata
  - Generate embeddings with a fashion-CLIP model, ingest into OpenSearch.
- Ran an A/B test against the existing Google Shopping API experience.

**Result**
- Latency dropped from 700ms to 500ms; clicks improved 2x; product-to-watchlist improved 8x.
- Leadership committed to scale the platform across all 500M products.

---

## 7. Mentoring
**Situation**
- A junior engineer owned a pipeline job that was failing randomly. 

**Task**
- Unblock the engineer quickly without just taking over the fix, and prevent the same class of bug from recurring.

**Action**
- Paired with the engineer to find the root cause, rather than debugging it for them.
- Together, we found the real issues: missing null-handling checks and an inconsistent feature name between the two jobs.
- Taught them to write small, isolated test cases that could reliably reproduce the bug.
- Coached them on how to communicate the issue and proposed fix to the upstream product team

**Result**
- The engineer shipped the fix themselves, and the pipeline stabilized. 
- They came away able to debug that pipeline independently going forward. 
- At the team level, we added schema validation checks into the jobs, which cut down on similar incidents afterward.

---

## Open Gaps — No Story Fits Yet

- **Keep healthcare human:** none of the above stories touch empathy vs. engineering efficiency trade-offs in a way that's genuine. Don't force-fit one of these — prep a separate, honest story before the real interview, even a smaller one.
- **Embrace a service mindset:** Shop the Look can stretch to cover this (helping a PM's idea succeed outside your own scope) if it comes up, but it's not a clean fit. Worth having something sharper in your back pocket.

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
