# Youtube Video Search

## Requirements & Scope
- Business Objective
    - Provide highly relevant search results
- Functionality
    - Given a text query, find the most relevant videos
- Constraints
    - 1B video to search from
    - Under 300ms latency

## Framing the ML problem
- ML Objective
    - Surface most relevant videos
- Input/Output
    - Search query -> text
    - Ranked videos
- ML Category
    - Statistical
        - Inverse Index
            - BOW
            - TF-IDF
            - Word2Vec
            - Contextual Embeddings
    - Supervised Learning
        - 100M text and video pairs

## Data Preparation
- Data Engineering
    - Data Source
        - Search Queries
        - Videos
        - Search query to Video Selected to be watched
    - Data Storage
        - SQL
            - Search Queries
            - Videos watched
        - NoSQL
            - Videos in object storage
- Feature Engineering & Transformation
    - Video Embeddings
    - Search Query Embeddings

## ML Modeling
- Model Selection
    - Statistical
        - Inverted Index
            - For every text and description of video, create a word -> video id mapping
    - Visual Search
        - Video Understanding
            - Frame-by-Frame understanding or Video Understanding
            - Average embeddings of all frames across the video to create Video embeddings
        - Embedd text query + videos for 100M pairs, calculate cosine similarity + labels to map embeddings to same space
    - Combine score from text search + visual search
- Model Training
    - Dataset
        - Text Search
            - Input
                - Video title and description
                - Data Normalization
                - Tokenization
            - Output
                - Word to document id
        - Visual Search
            - Input
                - Video
                - Title
                - Description
                - Creator
                - Tags
                - Raw Query
            - Output
                - Text and Video Embeddings
    - Loss function
        - Cosine Similarity
        - Softmax
        - Cross Entropy
    - Training from scratch or fine-tuning
        - Fine-tune the video embedding generation model

## Evaluation
- Offline
    - MRR
        - Rank of the most relevant video matters
- Online
    - CTR
    - Dwell Time

## Serving
- Production Pipeline
    - Search Query -> Normalization -> Tokenization -> Fetch Video Ids
    - Search Query -> Text Embedding -> Cosine Similarity with Video Embeddings using ANN
    - Combine scores -> Rank Videos

## Monitoring