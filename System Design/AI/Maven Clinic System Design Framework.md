# AI System Design Framework — Maven Staff Round

---

## 1. Requirements & Scope
- Business objective
- Functionality
- Data sources
    - Sensitivity (PII/PHI)
- Constraints (HIPAA/SOC2, latency, doc size)

---

## 2. Framing the GenAI Problem
- Input/output
- Generation vs RAG vs Agentic vs fine-tuning
    — Why not the alternative
- Hard guardrails 
    - Emergency/self-harm detection (Escalate to Human)
        - fast, cheap, pre-LLM check — never inside agent reasoning
        - regex or classifier (deterministic), not LLM
            - latency and unrealiable
        - Higher False Positive is better than False Negative
        - Cost to raise to human << Cost of missing an emergency

---

## 3. Architecture & Orchestration
- Workflow vs Single agent vs multi-agent — justified by task complexity
    - Lead agent routing to narrow specialists (appointments, provider search, health Q&A, support). 
    - Guardrails run before routing, not inside a specialist.
    - Single agent vs Multi agent
        - Rising turn counts - p99 increased pass a threshold
        - Increased Latency
        - Repeated tool-call - increase pass a threshold
- Tool set small on purpose 
    — Existing APIs become tools
    - User identity is injected by code
    - Design tool contracts in tool schema via hard dependency enforcement
- Loop stoppage conditions
    - task complete
    - max steps
    - repeated tool errors
    - low confidence 
    - safety trigger

---

## 4. Data Modeling & Knowledge Pipeline
- Chunking
    - Fixed
    - Overlapping
    - Semantic
    - Graph/Recursive (cross-reference)
- Embedding + vector DB (Pinecode, PGVector)
- Hybrid search (vector + BM25)
- Reranker
    - bi-encoder vs cross-encoder for top-k.
- Isolation
    - App-layer: metadata filtering at query time.
    - DB-layer: row-level security — a policy the database engine enforces on every query
    - Namespaces, separate stores (with different access controls and retention policy)
- Memory/state
    - Session state vs long-term memory
    - Conversation history + compaction

---

## 5. Evaluation & Quality
- Offline Evals
    - Golden dataset
        - Example
            - Input (message + conversation history) + Expected Path (tools used) + Expected Output -> Correctness (human labeled)
            - Including injection attempts ("ignore previous instructions")
            - Ambiguous cases ("I am bleeding")
        - Seeded and continuously updated from production traces
        - low-confidence + previously flagged
    - Metrics
        - Retrieval: Recall@K, Precision@K
        - Ranking: MRR, NDCG
        - Tool Calling: exact match on tool name + args (deterministic)
        - Generation: groundedness + correctness
        - Escalation: High Recall + Tracked Precision (catch all emergencies)
- Online Evals
    - LLM-as-a-Judge
        - Calibration
            - Human Labels with consensus (write rubrics for consensus)
            - Plot judge-vs-human agreement as a function of labeled sample size, and stop when the curve flattens
        - Pick or tune the judge against it
        - On sampled production data
        - Track judge/human disagreement rate to catch drift
    - Tool-call success rate
    - Escalation Agreement rate (Recall, Precision)
    - User Feedback (explict + implicit)
- Guardrails
    - Input filter (PII, injection, emergency)
    - Agent turn
    - Output filter (PII leakage, toxicity, factual consistency)
    - Defined fallback when a guardrail blocks a response

---

## 6. Deployment & Monitoring
- Model swap gating
    - Offline eval against golden set (new model can't regress)
    - Online A/B or Shadow deployment
- Tracing on every request 
    - Tool calls
    - Latency (p50, p99)
    - Cost per request
    - Retrieval calls
    - Token usage
- Drift detection on input and output quality.
- Kill switch to disable a tool/feature without redeploy

---

## 7. API Design
- Orchestrator call downstream services (appointment lookup, provider search)
    - Sync vs async
    - Retries
    - Timeouts
- Contract shape
    - what does a tool call's request/response look like
    - Versioning as APIs evolve
- User identity/auth injected by code
- Failure mode: 
    - What happens if a downstream API times out mid-conversation 
    — Degrade gracefully or fail safe

---

## 8. Scaling
- Latency budget (target: a few seconds, chat feel)
    - p50/p99
    - where time actually goes (retrieval vs generation vs tool calls)
- Cost per request
    - model routing (cheap/fast model for real-time chat vs stronger model for complex reasoning)
- Caching
    - repeated queries
    - embedding cache
    - prompt caching for static system prompt

---
