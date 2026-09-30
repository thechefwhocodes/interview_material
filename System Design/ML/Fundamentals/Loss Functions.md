# Loss Functions

## Cross Entropy Loss (CE Loss)

- Softmax
  - Convert raw logits into probablity
- Loss = -ve log prob of the ground truth

## Normalized Cross Entropy Loss

- Divides the Cross Entropy Loss value of the model by CE Loss of baseline
- If value is less than 1, the new model is better than baseline
- If value is larger than 1, the model couldn't outperform baseline

## Contrastive Loss

- Use raw logits
- Loss = (Lp) ** 2 + (margin - Ln) ** 2 -> Lp = Logit of positive class, Ln = Logit of negative class
- Move positive pairs close and negative pairs far by at least "margin"
- Cons
  - Have no notion of relative distance between positive and negative pair

## Triplet Loss

- Use raw logits
- Loss = max(0, Lp + margin - Ln)
- Calculates the pair wise distance between positive and negative pairs
- Ensure postive and negative pairs are seperated by at least "margin"

## Focal Loss

- Built on top of Cross-Entropy Loss
- Dynamically reduces the weight of easy-to-classify examples (majority class) and forces the model to concentrate on hard, misclassified ones

## MSE or MAE

- Used by regression models
- Penalize underestimation and overestimation equally
  - Actually value is 300
  - Will penalize 200 and 400 equally
- Forces the model to predict the mean

## Quantile Loss

- Used by regression models
- Penalize underestimation and overestimation differently
- Uses a parameter alpha to penalize predictions differently
- For instance:
  - Alpha = 0.9 or 90%
  - True label for delivery ETA is 50 mins
  - For Prediction = 60 mins, loss = 0.1 _ (60 - 50) = 1 -> (1 - alpha) _ (y - true_label)
  - For Prediction = 30 mins, loss = 0.9 _ (50 - 30) = 18 -> alpha _ (true_label - y)
  - We penalize the underestimation (false hope that delivery will arrive soon) more compared to overestimation (conservative ETA)

---

# Optimizer

## SGD (Stochastic Gradient Descent)

- Uses fixed learning rate to update weights
- Required careful tuning of learning rate

## Adam

- Uses adaptive learning rate that converges faster

## WALS (Weighted Alternating Least Squares)

- Used only for matrix factorization
- Fix one embedding matrix (UE) and optimize the other embedding (VE)
- Fix the other embedding matrix (VE) and optimize the embedding matrix (UE)
- Repeat

---

# Activation Functions

## Sigmoid

Map logits between (0, 1)

- Sigmoid and Softmax over two classes is mathematically identical

## TanH

Map logits between (-1, 1)

## Relu

Map logits between (0, 1). But all negative values are mapped to 0

---

# Regularization

## L1

## L2
