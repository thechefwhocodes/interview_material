# Shopify System Design

---

## Product Classification

### Requirements

- Classify products and map them to thousands of category taxonomy
- Data
  - Fixed taxonomy
  - Millions of new products need to be classified and mapped to category taxonomy in real-time
  - Current products are manually mapped by merchants and may not be highly accuracy
  - 10Ks of human-labeled examples
- Constraints
  - New leaf categories can be added dyanmically
  - Near-Real time system since we need to suggest categories to merchant

### Frame the ML Problem

- Classify the product to one of the taxonomy tree
- Data
  - Input
    - Product title, description, images
  - Output
    - Category taxonomy tree

### ML Models

- Logistic Regression with TF-IDF
  - Input
    - TF-IDF vector for the new product
  - Output
    - Multi-Class label spanning 10Ks of categories
  - Pros
    - Easy to train
    - Interpretable
    - Fast
  - Cons
    - Feature interaction is limited
    - Classes with very little data, hard to train for those
    - Have to train from scratch for new categories added

- Multi-Task Model with Shared Layer
  - Input
    - Embedding vector based on product title, description and images
  - Output
    - Task-specific classifier
      - Each classifier is trained to predict a specific level
      - Lower level classifier are trained to learn from upper level classifiers
  - Pros
    - Easier to train as compare to training 5 different classifiers for every level
    - Leaf nodes with less data learn from upstream classifiers
  - Cons
    - Cascading error
      - Error made with L1 prediction propogates to predictions in L2, L3 ...

- Two-Tower Model
  - Model product and category encoder
    - Product encoder
      - Initial concatanated embedding from product title, description and images
    - Category Encoder
      - Encode category as text
        - Appear -> Footwear -> Shoes -> Running Shoes
      - Convert text to embedding
  - Loss Function
    - Contrastive Loss
  - Hard Negative
    - Pick sibling of the actual taxonomy
  - Training
    - Train on the noisy data
    - Fine-tune on the small subset of human labelled data
    - Test on the remaining data
  - Inference
    - Pick top 20 taxonomy tress based on similarity between product and category
    - Cross-Encoder reranker to rank options
  - For new categories
    - Encode them using category encoder and make them available to be picked by the NN algo
  - Deployment
    - Shadow
    - A/B test

- Vision Language Model
  - FP8 quantization
  - KV caching

### Evaluation

- Micro vs Macro Precision, Recall, F1
- Hierarchical Precision, Recall, F1
- Online
  - %age of times merchant accepted the suggestion
  - %age of times merchant corrected or disregarded the suggestion

---

## Fradulent Activity Detection

### Requirements

- Detech fradulent order and raise them to merchant
- Score each checkout order. If score is higher than a threshold, auto cancel, otherwise, ask merchant to have a look
- Data
  - Only 0.5% of the 1M transactions every day are fradulent
- Constraints
  - Real-time system

### Framing the ML Problem

- Classify every order and output a score of it being fradulent or not
- Data
  - Input
    - User information
      - User IP
      - Account age
      - Current location
      - Previous order's average cost value
      - Category of items in the order
      - Previous payment methods
        - Bank
        - Country
        - Device
      - Saved address
    - Context
      - Cart total
      - Category of items in cart
      - Payment method being used
        - Bank
        - Country
      - Shipping address
  - Output
    - Probability Score of transaction being fradulent
  - Downsample -ve examples
    - Changes the data distribution
    - Inflates the raw score (model thinks an activity is more probable to be fraud)
    - Ranking still works
    - Fix
      - Re-calibrate the raw score to get true real-world probability using keep rate

### ML Models

- Logistic Regression
  - Fast, Interpretable
  - No feature interaction

- Decision Tree
  - Fast, Interpretable
  - Decision bounderies are parallel to the axis of the feature vector

- XGBoost
  - Fast, Interpretable
  - No continuous learning
  - Will have to training nightly on new data

### Inference

- Predict the risk score for every order
- Calculate the $$ value of the fraud
  - 4% risk on $2000 order is worse than 20% risk on $15 order
- Threshold using the $$ value

### Other

- If the merchant block fradulent activties, we may run out of +ve examples to train the next generation of model
  - To get around it, we let 1-2% of the fradulent orders through
  - Or we raise low-confidence score to human labelers, and yes that for training the model
- For new users, let the gap features be gap, use other features

---

## Search Relevance

### Requirements

- Provide more relevant search experience, currently search results are dominated by popular items
- Functionality
  - Search by text only
  - Increase diversity of the products for a merchant on the search page
- Data
  - 100Ks product per merchant
  - Human labeled data for relevance can be made available
- Constaints
  - Real-Time system

### Frame the ML Problem

- Provide relevant search results for a given text query
- Data
  - Input
    - Text Query
  - Output
    - Ranked list of products
  - Context
    - Millions of products along with text, description and images

### ML Model

- Inverted Index with TF-IDF and BM25
  - Keyword based search
- Vector Search
  - Two-Tower model to fetch embeddings for query and products
- Ranker
  - XGBoost model trained on relevance or CTR + PTR with manual levers

### Hard cases

- Cold Start
  - Use context based features
    - Embeddings
  - Don't interpolate CTR and PTR information for new items
  - Use exploration techniques
- Relevant Results
  - Random Exploration
  - Thompson Sampling
  - Inverse Propensity Scoring to get relevance
    - Use the loss function with weight for the item

### Evaluation
