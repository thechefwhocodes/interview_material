# Evaluation Metrics

## Offline

### MRR

- Consider the rank of the first relevant item
- If there are more than one relevant items in the list, they won't be considered

### Recall@K

- Among all relevant items available, how many of them made there way to Top K
- If number of relevant items are very large (millions), Recall@K will be a very small number
- It doesn't consider the rank of relevant items among Top K

### Precision@K

- Among Top K items, how many of them are relevant
- It doesn't consider the rank of relevant items among Top K

### mAP

- Mean of the Average Precision
- Take the average of the AP across multiple ranked list

#### AP

- In the list of K items, we calculate Precision@i at different values of i such that i'th item is relevant to the user
- We take the average of all of such Precision@i
- Eg:
  - Ranked List: [Relevant, Non-Relevant, Non-Relevant, Relevant, Non-Relevant]
  - We calculate Precision@1 and Precision@4 because 1st and 4th items are relevant
  - Precision@1 = 1, Precision@4 = 2/4 = 0.5
  - AP = (1 + 0.5) / 2 = 0.75

### nDCG

- Meausres the ranking quality of the output list compared to the ideal rank
- Divides the DCG of the output list by the ideal DCG of the ground truth list

#### DCG

- Take the ground truth relevance score (rel(i)) of each item in the list
- Divide rel(i) by the item's position in the output list (log(+1) - where i is the position of the item in the output list)
- Averages out all these scores for every item in the output list
- Eg:
  - Ranked List with Relevance Score: [0, 5, 1, 4, 2]
  - DCG = 0/log(2) + 5/log(3) + 1/log(4) + 4/log(5) + 2/log(6) = 6.151
  - Ideal Ranked List = [5, 4, 2, 1, 0]
  - Ideal DCG = 5/log(2) + 4/log(3) + 2/log(4) + 1/log(5) + 0/log(6) = 8.954
  - nDCG = DCG / Ideal DCG = 6.151/8.954 = 0.6869

## Online

### CTR

- Number of Clicked Items / Total Number of Suggested Items
