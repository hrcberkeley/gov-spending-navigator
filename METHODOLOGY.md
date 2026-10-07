# Methodology: how we found the AI awards in FY2026 federal spending

This text is written in Simplified Technical English (ASD-STE100).

## 1. Purpose

We want to find all federal awards in fiscal year 2026 (FY2026) that are related to artificial intelligence
(AI). An award is a contract or a grant. For each AI award, we also want to know the role of the recipient.

## 2. Source data

All data comes from USAspending.gov, the public database of federal spending.

| File | Content | Date of download |
|---|---|---|
| FY2026_All_Contracts_Full_20260906.zip | All FY2026 contract transactions to 6 September 2026 | 3 October 2026 |
| FY2026_All_Assistance_Full_20260906.zip | All FY2026 grant and other assistance transactions to 6 September 2026 | 3 October 2026 |
| Sept2026_PrimeTransactions.zip | All transactions with a September 2026 date | 3 October 2026 |
| defense_late_FY2026_current.zip | Department of Defense transactions from June to September 2026 | 5 October 2026 |

Each award can have many transactions. Each transaction can have a different description. We keep each pair
of recipient and award as one record. This gives 5,189,400 records. The records contain 3,971,239 different
description texts.

## 3. The starting list of known AI awards

Before this method, we made a list of AI providers and their AI awards. The list contains 14,376 awards.
A keyword search with a set of point rules selected 14,014 of these awards. Language-model agents examined
365 awards one by one. Each of these 365 awards pays for AI research, development, integration, operation,
or the supply of an AI product of the recipient. (3 awards are in both groups.)

In this method, we use the starting list for two purposes only:

1. The list shows the type of description that AI awards have. We use it to find similar awards.
2. The 365 awards that were examined one by one are a test set for the decision model.

## 4. The map of all awards

We put all records on a map. On this map, awards with similar descriptions are near each other.

1. We changed each description into a vector of numbers with the model BAAI/bge-base-en-v1.5. The model reads
   the first 256 tokens of each text. A token is approximately one word or one part of a word.
2. For each award, we calculated the mean of the vectors of all its descriptions.
3. We decreased the vectors to 64 dimensions with principal component analysis (PCA).
4. We decreased the vectors to 2 dimensions with UMAP. This gives the position of each award on the map.

We divided the awards into 1,500 small topics with k-means clustering. We put these topics into a tree of
larger topics. Each larger topic contains a maximum of 8 smaller topics. We found the typical words of each
topic with c-TF-IDF. A local language model (Qwen 3.5, 9B) wrote a short name for each topic. The names help
the reader. The typical words are the evidence.

## 5. The awards that we examined

We did not examine all awards. We selected the awards that can possibly be related to AI:

1. For each award, we found the 5 nearest awards in the starting list. We calculated the mean cosine
   similarity of the vectors. This is the "AI similarity" of the award.
2. We kept the 500,000 awards with the highest AI similarity.
3. We added all awards that contain an AI word. There are 99,657 of these awards.
4. We added all awards from the starting list.

This gives 611,344 awards. We call these awards "in scope".

We tested this selection before we used it. We removed some awards from the starting list. Then we did the
selection again. The 500,000 nearest awards contained 99.8% of the removed awards.

## 6. The decision for each award

The model Jev 1.13 from TypeSafe examined each award in scope one time. Jev gives a probability for each
possible answer. It does not write text.

We gave Jev the description of the award. Long descriptions can hide the AI part at the end. Thus, we gave
Jev the first 500 characters, and all the sentences that contain words related to AI. Examples of these
words are "machine learning", "neural", "computer vision" and "algorithm".

We asked Jev one question: is the work in this award related to AI, and if yes, how? The possible answers
are:

| Answer | Meaning |
|---|---|
| Develops AI | The recipient does research on AI, or makes or trains AI models or systems. |
| AI product | The agency buys or licenses an AI software product. |
| AI services | The recipient uses or connects AI for the agency. |
| Resells AI | The recipient supplies AI software licences or AI hardware. |
| AI-adjacent | AI is a small part of the work, or the work directly supports AI. |
| Not AI | The work is not related to AI. |
| Unclear | The description does not give sufficient information. |

An award is "AI-related" when the total probability of the five AI answers is more than 50%. The AI answer
with the highest probability gives the role. The cost of this step was 16.31 US dollars.

## 7. Tests of the decision

1. We gave Jev 100 awards from the test set (section 3). Jev identified 93 of them as AI-related. Jev
   identified most of the other 7 as "Unclear". These 7 awards are mostly administrative changes with no
   description of the work.
2. We gave Jev 400 random awards in scope. Jev identified 89% as not AI, 9% as unclear, and 2% as
   AI-related. We read the AI-related awards. Most of them were correct.

We also tested a local model (GLiNER2.5-Decide) with the same type of question. It identified only 63% of
the test awards. Thus, we did not use it.

## 8. Tests of the awards that we did not examine

We did not examine 4,578,056 records. We did these tests to find if we missed AI awards:

1. We took 1,000 random awards from each of three groups: the 50,000 awards immediately below the limit of
   AI similarity, the next 450,000 awards, and all other awards. Jev identified 2 of the 3,000 awards as
   AI-related. We read the 2 awards. Neither award is related to AI.
2. We took 20,000 random awards from all the awards that we did not examine. Jev identified 2 awards as
   AI-related. We read the 2 awards. Neither award is related to AI.

We found no AI award in 20,000 random awards. Thus, with 95% confidence, less than 0.015% of the awards that
we did not examine are related to AI. This is a maximum of approximately 700 awards.

## 9. Results

Each record has one of these statuses:

| Status | Records |
|---|---|
| AI-related | 18,001 |
| Needs a look | 72,017 |
| Not AI | 521,326 |
| Not looked at | 4,578,056 |

"Needs a look" has two causes:

1. The award is in the starting list (section 3), but Jev did not identify it as AI-related (4,454 records).
2. The description does not give sufficient information (67,563 records). Most of these records are
   administrative changes with a very short description.

## 10. Limits

1. Jev reads only the description. It does not read the amount, the recipient, or other sources.
2. A person must examine the records with the status "Needs a look".
3. A person must examine a random sample of the AI-related records. This sample will show how frequently Jev
   is correct. For example, a group of approximately 470 AI-related records is about Microsoft licences. Some of these records are possibly resale and not AI work.
4. The data does not include all subcontracts, all subgrants, or the members of consortia that get "other
   transaction" agreements. Defense data from July to September 2026 is possibly not complete yet.
5. The positions on the map are correct only for near awards. A large distance between two groups on the
   map does not have a clear meaning.
