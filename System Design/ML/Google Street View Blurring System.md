# Google Street View Blurring System

## Requirements & Scope

- Business Objective
  - Protect User Privacy by Blurring license plates and human faces
- Functionality
  - Detect license plates and human faces in the images
  - Blur them
- Data
  - 1M annotated data available
- Performance
  - Offline system
    - New images can be processed offline

## Framing the ML Problem

- ML Objective
  - Object Detection Problem
- Input/Output
  - Image
  - Detected objects with bounding boxes
- ML Category
  - Object Detection
    - Regression
  - Object Annotation
    - Classification

## Data Preparation

### Data Engineering

- Annotated Dataset
- Street View Images

### Feature Engineering

- Image Processing
  - Resizing
  - Normalization
- Data Augmentation
  - Random Crop -> Needs ground truth transformation
  - Random Saturation
  - Image Rotation -> Needs ground truth transformation
  - Veritical/Horizontal Flip -> Needs ground truth transformation
  - Change brightness, saturation or contrast

## Model Training

- Model Selection
  - Convolution Layer
  - Object Detection (Regression)
    - Region Proposal Network (RPN)
  - Object Annotation (Classification)
    - Neural Network
- Model Training
  - Loss Function
    - Regression
      - MSE
    - Classification
      - Cross Entropy Loss

## Evaluation

### Offline

- Intersection Over Union (IOU)
  - Area of Intersaction/Area of Union
    - Predicted vs Ground Truth
- Precision
  - Calculated over different IOU thresholds
- AP
- MAP

### Online

- Capture user reports and complaints

## Serving

- Overlapping Bounding Boxes
  - NMS (Non-Maximum Suppressions)
    - Get the all overlapping bounding boxes
    - Get one with highest confidence
- Pipeline
  - Offline
    - Images -> PreProcess -> Object Detection -> NMS -> Bluring -> Object Storage
