# AI System Design Interview Framework

## Requirements & Scope

- Business Objective
- Functionality
  - What functionalities does the system support?
    - Document Q&A
    - Generation (document, image, video)
    - Research
    - Action Execution
- Data
  - Knowledge Base: internal docs (PDFs, Images, API)
  - Data Sensitivity: PII
  - Compliance Constraints: HIPAA, SOC 2
- Constraints
  - Document size
  - Latency
  - Token budget/cost ceiling per request
  - Error handling
    - Explicit error vs Silent degradation vs Human in the loop?

## Framing the GenAI problem

- Define the product objective
  - Answer Accuracy
  - Automate a workflow
  - Generate Content
- System's Input and Output
  - Input
    - Natural Language Text/Voice
    - Documents
    - Images
  - Structured Output
    - With Citation
- Choosing the right GenAI pattern
  - Pure Generation
  - RAG: when answer needs to be grounded
    - Metadata filtering to retrieve documents
    - Isolation enforcement at database layer as mandatory filter
  - Agentic (Single vs Multi-Agent - task complexity)
    - Multi-step reasoning
    - External actions or API calls
  - Fine-tuning vs Prompting: needed when domain vocabulary can't be achieved via prompting alone
- Build vs Buy
  - Use frontier model APIs vs self-host open-weight model
  - Self-host when data can't leave for compliance reasons

## Knowledge & Context Pipeline

- Data Engineering
  - Ingestion source (databases, documents, file uploads)
  - Preprocessing (parsing PDFs/HTML, OCR, image segmentation)
    - Data Extraction
    - Data Cleaning and Normalization
    - Chunking Strategy
      - Fixed
      - Overlapping
      - Graph Based/Recursive
- Embedding & Indexing
  - Embedding models
  - Vector DB (Pinecone, PGVector, FAISS)
  - Hybrid Search (Vector search vs BM25)
- Retrieval
  - Top-k retrieval, re-ranking (cross-encoder vs bi-encoder reranker)
  - Metadata filtering (date, source, priority)
  - Access control to avoid un-authorized users access data
- Context Assembly
  - Prompt construction

## Architecture vs Orchestration

- Orchestration
  - Single LLM vs Workflow vs Multi-Agent
  - Control flow lives in orchestrator
  - Tool Call design
  - Loop Stoppage
    - Task Completion
    - Max Steps
    - Tool-Call return error (multiple time)
    - Confidence drop below threshold
    - Safety trigger fires
    - Classifier which decided conversation is done
- Model Selection
  - Frontier model vs Smaller models
  - Model routing
    - Cost vs Performance
    - Planning vs Execution
- Prompt Engineering
  - System Prompt with few-shot examples and caching strategy
  - Structure output
- Memory & State
  - Conversation history, compaction logic
  - Session state vs long-term user memory

## Evaluation

- Offline Evaluation
  - Golden Dataset
    - Update with sampled production traffic
    - Include injection attempts
    - Include low-confidence/flagged traces
      - Add the expected output (tool call, tool args, generation)
      - Trigger the pipeline to add the data point to golden dataset
      - Included for regression test
  - Metrics
    - Retrieval
      - Recall@K, Precision@K
    - Ranking
      - MRR, NDCG
    - Generation
      - Groundedness, Correctness, Completeness, Confidence
- Online Evaluation
  - LLM-as-a-Judge on %age of traces
  - Human in the loop when confidence is low or stakes are high (using classifier)
  - User feedback signals
    - Explicit Feedback (thumbs up/down)
    - Implicit Feedback (edit rate, abandoned conversations)
- Guardrails
  - Treat all non-system text as data, never as instructions
  - Classifier on Input (PII detection, prompt injection, emergency detected)
  - Classifier on Output (PII leakage, toxic content, factual consistency)
  - Input filter pass → Agent Turn → Output filter pass
    - Fallback behavior when a guardrail blocks a response

## Deployment & Serving

- Model Swap Gating
  - Offline Gate
    - Golden dataset (Satisfied)
      - Read Input
      - Retrieved Context
      - Expected Output
    - Run the agent against the golden dataset and measure the metrics
    - New model score should not dip below old model score
  - Online Gate
    - Traffic Mirror
    - A/B testing
    - Measure same metrics
- Rollback and Kill Switch
  - Disable a tool or feature without redeploy

## Monitoring & Observability

- Logging and Tracing
  - Full request tracking
    - Retrieval calls, tool calls, reasoning steps, token usage
- Drift Detection
  - Output Quality drift
  - Input Data drift
- Operation Metrics
  - Latency (p50/p99)
  - Throughput
  - Cost per request
  - CPU/GPU utilization
- Feedback Loop
  - User Signals
