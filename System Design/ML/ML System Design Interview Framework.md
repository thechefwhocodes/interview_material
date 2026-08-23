# ML System Design Interview - Framework

## Clarify Requirements & Scope

- Business Objective
- Functionality
  - What features should the system support
- Data
  - What is the data source
  - How big is the data
  - Is data labeled
- Scale
  - How many users
  - How many items
- Performance
  - Real-Time system or Batch

## Framing the problem as ML task

- Define the ML objective
- Specify the system input and output
- Choosing the right ML category
  - Supervised
    - Regression
    - Classification
      - Binary
      - Multiclass
  - Unsupervised
    - Clustering
    - Next Token Prediction
  - Reinforcement Learning
    - RLVF
      - DPO
      - PPO
      - GRPO

## Data Preparation

- Data Engineering (Design and build pipelines for collecting, storing, retrieving and processing)
  - Data Source
  - Data Storage (SQL, NoSQL)
    - ETL
    - Data Type
      - Structured (stored in relational dbs)
        - Numerical
          - Continuous (House Prices)
          - Discrete (Number of houses sold)
        - Categorical
          - Ordinal (gender)
          - Nominal (not happy, neutral, happy)
      - Unstructured (stored in non-relational dbs)
        - audio, video, text, images (stored in data lake like GCS)
- Feature Engineering
  - Requires domain knowledge to select and extract features from raw data
- Feature Transformation (for model to use them)
  - Handling Missing Values
    - Deletion (row or column)
    - Imputation (replace with default or mean, median, mode)
  - Feature Scaling
    - Normalization (Min-Max Scaling)
    - Standardization (Z-score normalization)
  - Bucketing
  - Encoding Categorical features
    - Integer Encoding (Bad -> 0, Good -> 1, Excellent -> 2)
    - One-hot Encoding (Red -> [0, 0, 1], Green -> [0, 1, 0], Blue -> [1, 0, 0]) - use when no relationship between categorical features
    - Embedding Encoding

## Model Development

- Model Selection (Linear Regression, Logistic Regression, Decision Trees, Gradient Boosting Decision Trees and Random Forest, SVM, Neural Network, Deep Neural Network, Transformers)
- Model Training
  - Constructing the dataset
    - Raw Data
    - Feature and label engineering
    - Sampling strategy
    - Split the data
    - Address class imbalance
      - Undersample vs Oversample
      - Altering the Loss function
  - Choosing the loss function (Cross Entropy vs MSE)
  - Training from scratch vs fine-tuning
  - Distributed Tuning
    - Data vs Model Parallelization

## Evaluation

- Offline Evaluation
  - Classification
    - Accuracy, Precision, Recall, F1, Confusion Matrix, AUC
  - Regression
    - MAE, MSE, RMSE
  - Ranking
    - Recall@K, Precision@K, MRR, NDCG
  - Natural Language
    - BLEU, ROUGE
- Online Evaluation
  - Ad-clicks
    - CTR, PTR, Revenue Lift
  - Recommendations
    - CTR, Total watch time, # of completed videos

## Deployment and Serving

- Cloud vs On-Device
- Model Compression
  - Knowledge Distillation: train a smaller model to mimic a larger model
  - Pruning: find the least useful parameter and set them to zero
  - Quantization: use fewer bits to represent the parameters
- Productionizing
  - Shadow deployment: Traffic goes to both models, but only original model return response to user. The new model's response is captured but not returned
  - A/B testing
- Prediction Pipeline
  - Batch vs Online prediction

## Monitoring Infra

- System failure
  - Data drift
- Monitor
  - Operation related metrics: Latency, Throughput, # of prediction requests, CPU/GPU utilization
  - Data Drift, Model Accuracy, Model Version

## Question

- Model training
  - L1 vs L2 Regularization vs K-fold CV vs Dropout
  - Stochastic Gradient Descent (SGD) vs Adam
  - ReLU vs Sigmoid vs Tanh
  - Bias vs Variance
  - Underfitting vs Overfitting
  - Knowledge Distillation
  - Ranker vs ReRanker
  - Online feature computation vs Batch feature computation
