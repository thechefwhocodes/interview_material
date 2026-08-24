# Amazon Leadership Principles

## Stories

---

### Phia Search Platform

**Situation**
- At Phia, the search experience ran entirely on the Google Shopping API.
- There was no control over personalization.
- And premium partners were complaining that their products were being surfaced next to products from  marketplace like eBay and Poshmark, which hurted their brand reputation.

**Task**
- The leadership was hesitant to invest in building an in-house search platform.
- They were worried that it could take months of engineering effort without any guarantee that it would actually work
- For them the safer option was to just keep the degraded experience.

**Action**
- I proposed we start with just the shoe category, which involed 5M products instead of 500M.
- And built a real end to end pipeline to test whether an in-house system could actually deliver a better experience.

**Result**
- The latency dropped from 700ms to 500ms
- Clicks and Product to Watchlist eight 2x and 8x respectively.
- This proved to the leadership that building an in-house platform across 500M products is essential to provide better customer experience.

---

### Open Search Partitioning
**Situation**
- At Phia, as we were scaling the search platform, the CTO, wanted to partition our OpenSearch cluster into physical indexes per category and brand, to reduce query latency by shrinking the search space.

**Task**
- I disagreed, because managing hundreds of physical indexes as we added more brands and verticals would have created significant infrastructure and DevOps overhead. 
- I proposed we go with logical partitioning through metadata and pre-filtering ANN search
- But he was skeptical of my approach, because in his understanding, since ANN algorithm would still be navigating a similarly sized vector space, the latency wouldn't meaningfully improve. 
- This wasn't a case of him dismissing an idea, he had a real technical reason to doubt it.

**Action**
- Instead of going back and forth on whose intuition was right, I proposed we settle it with a benchmark on real production data. 
- I ran both approaches head to head, physical indexes per brand versus logical indexes with pre-filtering.

**Result**
- The benchmarking results, showed latency difference was negligible. 190ms versus 200ms. 
- This let us choose the logical indexing approach with confidence, avoiding the engineering overhead of managing physical indexes at scale.

---

### Context Management and Memory Layer
**Situation**
- At Bezi, the leadership (including CEO, CTO, and product lead) had a hypothesis, inspired by tools like Obsidian.
- They envisioned that context management and memory layer could make coding agents more accurate. Along with making them cheaper and faster. 
- They wanted to pass on these savings to the customers.
- And wanted to build a product around that idea.

**Task**
- I was hired specifically for this project.
- There was no clear direction on how to build it.
- So I decide to validate their hypothesis, and share the finding with the leadership.

**Action**
- Instead of jumping straight to a monthslong build, I proposed running POCs first.
- I researched context management and memory layer approaches.
- I picked real projects with actual usage in collaboration with GTM.
- And ran a controlled comparison, involing coding agent with context management and memory layer versus without it.
- The leadership pushed back on my initial methodology. 
- They wanted broader coverage across projects of different complexity.
- I incorporated that and expanded the testing.

**Result**
- Context management and memory layer did improve accuracy, cutting the number of turns needed by 30%.
- But it did not reduce latency or cost, because carrying that context across a session adds up in tokens.
- Even after retesting across more projects and complexity levels, the result held.
- I had to tell leadership their hypothesis didn't hold up.
- They were disappointed, but the finding was solid and saved the company months of misdirected build effort.

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
- I got them to volunteer their time, and led that small cross functional group of two ML engineers, two iOS engineers and a designer, to build the whole flow in under a week.

**Result** 
- We shipped a working end to end experience inside the eBay app.
- We tested with an internal users, and pitched it to leadership.
- They got excited to put it on the next quarter's roadmap. 
- The project is live today as Shop the Look. And we also filed a patent on it.

---

### Feedback

**Situation**
- At eBay, I was leading a team of 6 engineers which was a cross-functional team, building the next generation recommendations platform.
- I used to gave weekly updates to a stakeholder group that included senior engineers, two directors, and a VP.

**Task**
- After one of these updates, my director gave me a direct feedback that my communication was too technical for the VP.
- I was spending too much time on implementation details and technical comparisons, and not enough on high level progress.

**Action**
- I took that feedback and restructured my next updates.
- I shifted away from technical difficulties, towards
  - Overall Architecture
  - Milestone progress against the roadmap
  - And testing status framed at a level that VP could track without needing engineering context.

**Result**
- Engagement from the VP changed visibly.
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
