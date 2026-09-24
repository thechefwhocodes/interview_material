# Loss Functions

## Cross Entropy Loss

- Softmax
  - Convert raw logits into probablity
- Loss = -ve log prob of the ground truth
- Cons
  - Only move positive example close to the query
  - Doesn't move negative examples far away

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
