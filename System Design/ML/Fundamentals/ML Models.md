# Machine Learning Models

## ML Category

- Supervised Learning
  - Regression
  - Classification
    - Binary
    - Multi-class
    - Multi-label
- UnSupervised Learning
  - Clustering
  - Generation
- Reinforcement Learning
  - RLHF
  - DPO

## Contrastive/Representation Learning

This model is used to train embedding models to have embeddings of postive pairs close and negative pairs further away

### Models

- Images
  - ResNet (CNN)
  - ViT (Vision Transformer)
- Text
  - BERT

### Dataset Prepartion

Annotate positive and negative pairs

- Human Judgement
  - Cons
    - Manual Process
- User Interaction
  - Pros
    - Automatic Data Generation
  - Cons
    - Data is noisy
      - Not representative of groud truth
      - User click on items which are not similar
    - Sparse Data
      - Click data may not be available for every items
- Data Augmentation
  - Pros
    - No Manual work
    - Not noisy
  - Cons
    - Real data may differ from augmentated data

### Training

- Convert inputs into embedding vector
  - Indiviually convert the anchor (A), positive (P) and negatives (Ns) into embedding vectors
    - embedding_A = model(A) -> shape [1 x dim_size]
    - embedding_P = model(P) -> shape [1 x dim_size]
    - embedding_Ns = model(Ns) -> shape [N x dim_size]
- Use similarity score between two embedding vectors to rank the items
  - Ecludian Distance
    - Suffers from curse of dimentionality
  - Dot Product
    - Takes vector length into consideration
    - A.B = |A||B|.cos
  - Cosine Similarity
    - Only meausre the angle between the two vectors
    - (A.B)/(|A||B|)

### Loss Calculation

2 step process

- Computer Similarity
  - Between every anchor and candidate embedding vector
  - We should have N+1 similarity scores (SS)
- Loss Calculation
  - Cross Entropy Loss using SS
  - Contrastive Loss using SS
  - Triplet Loss using SS

### Evaluation

- Offline
  - Recall@K
  - Precision@K
  - MRR
  - mAP
  - NDCG
- ## Online
  - CTR

### Inference

- Given the image, generate the embedding vector using the deployed ML model
- Use Nearest Neighbor to find similar emeddings
  - KNN
  - ANN
- ReRanker
  - Post filtering using Business Logic

### Deployment

- Triton + ONNX

## Inverted Index

This model is used to find documents relevant to the text query. It's a statistical model and not a ML model

### Models

- ElasticSearch
  - Built using Apache Lucene

### Data Preparation

All documents are required to build the inverted index

### Training

Documents are processed through a pipeline with following steps:

- Text Normalization
  - Lower casing
  - Punctuation Removal
  - Triming Whitespace
  - NFKD
  - Stripping Accents
  - Lemmatization and Stemming
- Tokenization
  - Break down text into smaller units called tokens. This includes
    - Word Tokenization
    - Subwork Tokenization
    - Character Tokenization
- Index Creation
  - {token -> list[tuple]}
  - Unique Token points to list of
    - (document ID, term frequency, position offsets)
      - document ID -> ID of the document which contains the token
      - term frequency -> frequence of the token in the document
      - position offset -> index at which the word sits in the document
  - Inverse Document Frequence (IDF)
    - IDF(t) = log(N/DF(t))
      - N = Number of documents
      - DF(t) = Number of documents containing the token

  ### Inference
  - Retrieval
    - Query is normalized and tokenized
    - Documents are fetched which contains at least one (or all) of these tokens
  - Ranking
    - For every document, engine compute
    - Score(D,Q) = Sum of all tokens in query (TF(t, D)) \* IDF(t)
