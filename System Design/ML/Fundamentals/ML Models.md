# Machine Learning Models

---

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

---

## Recommendation Systems

- Personalized
  - Content-Based Filtering
  - Collaborative Filtering
  - Hybrid Filtering
  - Ranking
    - Learning to Rank
      - Pointwise
      - Pairwise
      - Listwise
      - GNN
- Non-Personalized

---

## Representation Learning

This model is used to train embedding models to have embeddings of postive pairs close and negative pairs further away. The pairs can be (text, text), (text, image), (image, image) etc.

### Models

- Images
  - ResNet (CNN)
  - ViT (Vision Transformer)
- Text
  - BERT
  - GPT3

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
  - Indiviually convert the Query (Q), positive (P) and negatives (Ns) into embedding vectors
    - embedding_Q = model(Q) -> shape [1 x dim_size]
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
  - Between every query and candidate embedding vector
  - We should have N+1 similarity scores (SS)
- Loss Calculation
  - Cross Entropy Loss
  - Contrastive Loss
  - Triplet Loss

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

---

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
  - BM25
    - Takes document length into account as well

---

## Text Encoder

### Bag of Words (BoW)

#### Data Preparation

- Collect all the documents in the corpus
- Normalize
- Tokenize

#### Training

- Get a sorted list of unique tokens in the corpus
- Map each token to it's index in the sorted list

#### Inference

Given a query

- Normalize
- Tokenize
- Create a vector with count of the token index = the number of occurance of token in the sentence

#### Verdict

- Pros
  - Simple
  - Fast
- Cons
  - Word order is not considered
    - Let's watch TV after work
    - Let's work after watch TV
    - Both of the above leads to same vector
  - Semantic meaning is missing
  - Representation is very sparse

### TF-IDF (Term Frequence-Inverse Document Frequence)

- It reflects how important a word is to a document in a collection
- Similar how BoW works but also reflects the frequence of the token in the document

#### Verdict

- Cons
  - Needs to be recomputed when a new document is added
  - Order of the words/tokens is not considered
  - Semantic understanding is missing
  - Representation is very sparse

### Word2Vec

Uses co-occurrences of words in the local context to learn word embeddings

#### Models

- Continuous Bag of Words (CBOW)
- Skip-Gram

#### Verdict

- Embeddings are statis and not contextual
  - "Bank" will have same embeddings irrespective of "River Bank" or "Financial Bank"

### Transformer-based Architecture

The model uses the context of the words in the sentence when converting them into embeddings. They produce different embeddings for same word depending on the context (unlike Word2Vec).

#### Models

- BERT
- GPT3

---

## item Encoder

### item-level models

Process a whole item to create an embedding

### Frame-level models

- Proprocess a item and sample frames
- Run the model on sampled frames to create frame embeddings
- Aggregate frame embeddings to generate the item embeddings

#### Verdict

- Pros
  - Faster than item-level models
  - Less expansive than item-level models
- Cons
  - Can't understand the temporal aspects like actions and motion

---

## Classification

- Assign a label to the input data
- Label can be Binary (0/1) or Multi-Label (data can belong to multiple classes)

### Single Binary classifier

- We model the input to a binary outcome

### One binary classifier for each class

- We model one binary classifier for each class
- We can monitor and improve different models independently
- But the cost of training and maintaining is high

### Multi-Label classifier

- The input may belong to multiple classes
- A single model is training to predict multiple classes

### Multi-Task classifier

- We train the model to learn multiple tasks simultaniously
- We use shared layers and task-specific layers to model multi-task classifier
- Shared layers are used to transform input features into new ones
- Task-Specific layers (classifier heads) are used to predict specific class
- Training and Maintaining model is not expensive
- Training data for each task contributes to the learning of other tasks

### Models

- Logistic Regression
- XGBoost
- Neural Network

---

## Content-Based Filtering

Uses item encoder and user encoder to calculate user and item embeddings and recommend similar items.

### Pros

- Ability to recommend new items
  - Not relying on the interaction data
- Ability to capture unique interests

### Cons

- Requires domain knowledge and feature engineering

### Model

- Two-Tower Model

### Data Preparation

- Extract features from different (user, item) pairs
- Capture both positive and negative pairs
  - Use explicit and implicit feedback to capture positive and negative pairs
  - We will have to use technique to tackle class imbalance problem
    - Downsampling -ve pairs
    - Weighted loss to penalize mistakes on +ve pairs heavily

### Training

- Encode user features using User Encoder
- Encode item features using Item Encoder
- Calculate similarity between user and item embedding
- Use Sigmoid to map similarity score between (0, 1)

### Loss Function

- Cross Entropy Loss to calculate loss
- Adam or SGD to optimize weights

### Inference

- Use ANN to fetch relavent items

---

## Collaborative Filtering

Uses user-user similarity to recommend items

### Pros

- No domain knowledge needed
- Easy to discover user's new area of interest
- Efficient

### Cons

- Cold-start problem: Limited data is available for new user and item
- Connot handle niche interests
- Only relies on interation data, can't use other features

### Model

- Matrix Factorization

### Data Preparation

We need to capture user's opinion regarding a item. We can do this by creating a user-item feedback matrix. We can collection 3 type of feedback

- Explicit
  - Capture interactions like
    - Like
    - Share
    - Comment
  - This feedback is very rare and make the matrix sparse
- Implicit
  - Capture
    - Clicks
    - Watch time
  - This data is available in abundance, leading to better model for training
  - This doesn't directly reflect user's opinion and might be noisy
- Combination of explicit and implicit
  - Combine both explict and implicit signals

### Training

Model objective is to maximize relevance.

- We build the feedback matrix FM of dimension UxV (U = # of users and I = # of items)
- We randomly initialize two random matrix UE (user embedding) and IE (item embedding) of dimension UxD and DXV (D = hyperparameter)
- _Note_
  - We can't use SVD (Singular Value Decompostion) to calculate UE and IE matrices because the FM is very sparse
  - SVD doesn't work well on sparse matrices

### Loss Function

- Squared distance over observed paris
  - Calculate the sum of squared distances over all pair of non-zero values in the feedback matrix
  - Loss function doesn't penalize the model for bad prediction over unobserved pairs
- Squared distance over both observed and unobserved pairs
  - Calculate the sum of square distances over all pair in the feedback matrix (zero and non-zero values)
  - Since the feedback matrix is sparse, so unobserved pairs dominate observed pairs during training
- Weighted combination of squared distance over observed and unobserved pairs
  - Observed pairs are weighted heavily compare to unobserved pairs
  - The weight W is hyperparameter which needs tuning

### Inference

- To calculate the relevance between a user and candidate items, we use similarity score

---

## Learning to Rank

Having a query and list of items, what is the optimal ordering of the items

### Pointwise

- For each item, predict the relevance between the query and the item using classification or regression.
- Score of each item is predicted independently of other items.
- Final ranking is achieved by sorting the predicted relevance scores.

### Pairwise

- We take two items and predict which item is more relevant to the query.
- Models
  - RankNet
  - LambdaRank
  - LambdaMART

### Listwise

- We take list of items and predict the idea ordering of the entire list
- Models
  - SoftRank
  - ListNet
  - AdaRank

### Models

#### Logistic Regression

- Models the probability of binary outcome using linear combination of features
- Use Sigmoid to map linear score between (0-1)
- Pros
  - Fast, interpretable
- Cons
  - Non-linearity can't be expressed
  - When multiple features are highly correlated, model can't learn the task well
    - To get around correlated features, we can use feature crossing
    - Manual process and requires domain knowledge

#### Decision Tree

- Use tree-like model for decision boundaries
- Pros
  - Fast, Interpretable
  - No data prep required
- Cons
  - Decision boundries are parallel to axes in feature space
  - Very sensitive to data variation

#### Random Forset

- Train multiple decision trees in parallel
- Each tree make prediction independently
- Voting mechanism is used to combine the predictions and make a final prediction
- To avoid all trees in the forset to be identical
  - Random subsample of original dataset is fed to each tree
  - Different decision trees are only allowed to look at randomly selected subset of features

#### Gradient-boosted decision tree

- We train several weak decision trees sequentially to reduce prediction errors
- Pros
  - Reduce bias and variance
  - Interpretable
- Cons
  - Lots of hyperparameter to tune: # of iteration, tree depth, regularization parameters
  - Doesn't work for unstructure data
  - No continuous learning

#### Neural Network

- Best for learning non-linear relationship between features
- Pros
  - Best for continuous learning
  - Works for unstructured data
- Cons
  - Large training data is required
  - Black-box nature
  - Expensive to train

#### Deep & Cross Network (DCN)

- Model is used to automatically learn feature interactions
- Uses Cross Network and Deep Network to learn complex feature interactions
- Cross Network
  - Feature interaction is explicity force through multiplication
  - If Layer 1 creates AxB, Layer 2 creates (AxB)xC
- Deep Network
  - Doesn't explicitly multiply features
  - Model approximate the concept of multiplication through layers of addition and non-linear combination
  - Relu(w1x1 + w2x2 + b)
- The output of Deep Network and Cross Network is concatenated to make final prediction

#### Factorization Machine (FM)

- Traditionally, to calculate feature interations
  - y = w0 + wi.xi + wij.xi.xj
  - Since data is extremely sparse, interations for xi and xj might be low, hence wij may be 0
  - Learning wij would be tough
- Instead of learning wij, FM tries to learn two vectors (vi, vj)
  - y = w0 + wi.xi + vi.vj.xi.xj

#### Deep Factorization Machine (DeepFM)

- It uses Deep Network with FM to train the model
- Features from FM and Deep Network are concatenated to make predictions

## Graph Neural Network

- We model the problem as a graph network
- Usually problem falls into three broad category
  - Graph-level prediction
    - Given a chemical compound, predict if it's an enzyme
  - Node-level prediction
    - Given a social network, predict if the user is a spammer
  - Edge-level prediction
    - Given a social network, predict if two users will form a connection or not

### Data Preperation

- Gather the data regarding
  - Users
    - Demographic data: eductation, work background, skills
    - Age, Interest etc
  - Connections
    - Who is connected of whom
  - Interactions
    - Who has interacted with whom
    - Who sent the request, who accepted it
    - Who posted and who reacted

### Training

- Create the initial snapshot of graph at time t
  - Train embedding of users so people who are connected have embeddings similar to each other
- Initial Embedding (h0)
  - Only utilize user's attributes not connection
    - Eduction, Work, Age, Interest etc
- Combine Initial Embedding with Neighbour's embeddings
  - Embedding for a user = w0.h0 + w1.h1 + w2.h2 + ....
  - hi = Average embedding of all ith connections
- Label = at time t+1, did two users form connection or not

### Loss Function

- Cross Entropy Loss
