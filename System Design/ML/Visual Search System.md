# Visual Search System

## Requirements and Scope

- Business Objective
  - Retrieve images similar to query images
- Features
  - Find similar images
  - Support Images Search only
  - User can select or upload an image
- Data
  - 1B Images in database
  - Data is not labeled
    - Only raw images
    - No metadata available
- Scale
  - Work for 1B images
  - 300ms end to end latency
- Performance
  - Real-time system

## Framing the ML Problem

- ML objective
  - Ranking Problem
    - Surface images which similar to given search query
- Input
  - Raw Image
- Output
  - Ranked Images
- ML Category
  - Representation Learning

## Data Preparation

- Data Sources
  - Can't Use
    - User data
    - User-Image interaction
  - Can Use
    - Image data
- Data Storage
  - Object Storage (S3 or GCS)
  - Unstructured data (images)
    - ETL
      - Extract images from object storage
      - Use techniques like data augmentation to build similar image pairs
        - Resizing
        - Scaling
        - Z-score normalization
- Feature Engineering & Transformation
  - Image Embeddings

## Model Development

- Model Selection
  - CNN (ResNet)
  - VisionTransformer (ViT)
- Model Training
  - Contrastive Learning
    - Query Image
    - One image similar to query image
    - A few dissimilar images
    - Model learns to produce embeddings which are close to each other for similar images than dissimilar ones
- Data Construction
  - Raw Data
    - Images
  - Feature and label
    - Query image
    - N other images
      - 1 similar to query image
        - Human Judgement
        - Clicks
          - Very noisy
          - Sparse
            - May not have click data for some images
        - Image Augmentation
      - N-1 dissimilar to that
    - Label
      - Index of the image similar to query image
  - Loss Function
    - Cross Entropy
      - For each row with query and candidates
        - Calculate cosine similarity between embeddings
        - Do a Softmax
        - And calculate Cross Entropy Loss between the Softmax scores and label
  - Training from scratch or fine-tuning, either works

## Evaluation

- Offline Evals
  - Classification
    - Not to use
      - Recall @ K
        - If there are only 5 relevant images in top 10 and we have 1000s of relevant images, this metric will be very small
      - Precision
        - This will only tell how many relevant images are in top 10
        - But ignores the rank
      - MRR
        - This only capture the rank of first relevant image
        - It disregards the rank of other images
    - Use
      - NDCG
        - This capture the rank of the all relevant images
- Online Evals
  - CTR

## Deployment and Serving

- On-Cloud Deployment
- Production
  - A/B test against existing solution
- Prediction Pipeline
  - Batch (all images in the catalog are already embedded)
  - A new image comes
    - Embed the image using ML model
    - Perform ANN search using HNSW to navigate 1B images during run-time, fetch top 500 images
    - Calculate cosine similarity between image and candidates
    - Return ranked result

## Monitoring

-
