# Training, Inference and Deployment

## Training

### Distributed Training

## Inference

### Nearest Neighbor

- Exact
  - KNN
    - Search the entire space, calculating distance of each vector with the query
    - Get K nearest points
    - Time Complexity: O(DxN) -> D = Dimension Size, N = total number of points
- Approx
  - ANN
    - Returns close enough items instead of exact nearest neighbor
    - Time Complexity: O(Dxlog(N))
    - Tree-Based ANN
      - Split the space into multiple partitions
    - HNSW (Hierarchical Navigable Small World)
      - Use a multi-level map
      - Searches start at a fixed entry point in the top layer
      - Greedily select the closest neighbor in the current layer
      - Descend to the next layer
      - Continue

## Deployment

- Deploy the ML Model
  - In-Memory using standard Pytorch / Hugging Face via FastAPI
    - Pros
      - Low Setup
    - Cons
      - Baseline PyTorch latency
    - Flow
      - Request hits Python Server
      - Python parses the request
      - Hands it to a heavy PyTorch wrapper
      - PyTorch performs calculations
      - Results travel back up the chain
  - ONNX runtime
    - Converts the model into a standardized, framework-independent C++ computational graph
    - Converts 32-bit floating-point weights to 8-bit integers
    - The math graph executes inside a highly optimized C++ engine
  - Triton
    - Take individual incoming API request arriving miliseconds apart
    - Batch them to maximize GPU utilization
    - Send to ML Model for inference
  - Performance
    - PyTorch < ONNX < Triton + PyTorch <= Triton + ONNX

## Continuous Learning

- Overheads of Continous Learning
  - Model gating
  - Model checkpointing
  - Rollback
  - Handling data quality issues during incidents
