# Methodology: how we found the AI awards and AI providers in FY2026 federal spending

This text is written in Simplified Technical English (ASD-STE100).

## 1. Purpose

We want to find all organisations that the US federal government paid for work related to artificial
intelligence (AI) in fiscal year 2026 (FY2026). We call these organisations "AI providers". We also want to
find all FY2026 awards that are related to AI. An award is a contract or a grant.

The work has two parts:

- Part A (sections 3 to 9): we made a list of AI providers. We examined recipients (organisations).
- Part B (sections 10 to 15): we examined each award one by one, and not only each recipient.

## 2. Source data

All data comes from USAspending.gov, the public database of federal spending.

| File | Content | Date of download |
|---|---|---|
| FY2026_All_Contracts_Full_20260906.zip | All FY2026 contract transactions to 6 September 2026 | 3 October 2026 |
| FY2026_All_Assistance_Full_20260906.zip | All FY2026 grant and other assistance transactions to 6 September 2026 | 3 October 2026 |
| Sept2026_PrimeTransactions.zip | All transactions with a September 2026 date | 3 October 2026 |
| defense_late_FY2026_current.zip | Department of Defense transactions from June to September 2026 | 5 October 2026 |

We also used the USAspending API to get the current record of each award that we added to the list.

Each award can have many transactions. Each transaction can have a different description. We keep each pair
of recipient and award as one record. This gives 5,189,400 records. The records contain 3,971,239 different
description texts.

# Part A: the list of AI providers

## 3. First search: keywords and scores

First, we used the USAspending API. We searched for these awards:

1. Contracts with an industry code (NAICS) for software and computer services.
2. Contracts and grants with AI words in the description.
3. Subcontracts with AI words in the description.

A set of rules gave each description a score:

| Group of words | Examples | Points |
|---|---|---|
| Group 1 | "artificial intelligence", "machine learning", "neural network", "large language model", "AI" | 3 |
| Group 2 | "computer vision", "natural language", "predictive analytics", "data science" | 2 |
| Group 3 | "robotic process automation", "GPU", "autonomous" | 1, only with a word from group 1 or 2 |
| Words of resale | "licence", "subscription", "renewal", "hardware", "resale" | minus 2 |

## 4. Second search: all transactions in the archives

The API search uses only some industry codes. Thus, we also examined all transactions in the archives, with
no limit on the industry. We gave each transaction the score of section 3. We kept the recipients with a
score of 3 or more. This gave 3,475 recipients.

## 5. Review of the recipients

Language models (Claude Haiku and Claude Sonnet from Anthropic) examined the recipients:

1. A model read the description of the awards of each recipient. It gave a label: AI provider, AI-adjacent,
   reseller, not AI, or unclear.
2. For each company with the label "AI provider", a model searched the web for evidence of AI work.
3. A stronger model examined the companies with weak evidence again.
4. We examined a sample of the results. Approximately 80% of the "AI provider" labels for companies
   were correct.

After this step, the list had 1,763 recipients: 966 companies, 524 universities, 271 non-profit
organisations, and 2 other organisations.

## 6. Problems with the first list

We examined the method and found these problems:

1. The penalty for resale words removed companies that sell their own AI software. For example,
   "licence for the Flyways AI platform" got a score of 1.
2. A description with only one word from group 2 (for example, "computer vision") got a score of 2. Thus,
   no person examined it.
3. The model examined each recipient from only three parts of text. One AI award of a recipient with many
   awards can be hidden.
4. Department of Defense data is published with a delay of up to 90 days.
5. The 80% test measured the correctness of the labels. It did not measure the number of providers that
   we did not find.

## 7. Expansion of the list

To correct these problems, we changed the rule for new providers. A score does not decide anymore. A new
recipient must have at least one specific award that pays for one of these types of work: research,
development, integration, operation, or test of an AI model or system, or the supply of its own AI product.
These types of work are not sufficient: resale of software from a different company, general data analysis,
AI education, and the use of AI by the recipient for its own work.

For each new provider, we kept the identifier of the award, the evidence, and the reason. We examined the
current record of the award in the USAspending API. The award must have at least one FY2026 transaction.

Then we did these searches:

1. We examined the archives again, with no resale penalty. Words of function and names of AI products can
   make a recipient a candidate.
2. Language-model agents examined 1,626 recipients. These recipients included 456 recipients that the first
   review rejected.

This step added 195 recipients. The list then had 1,958 recipients.

## 8. Search by similarity of meaning and by project identifiers

Some awards have AI work but do not use AI words. To find these awards, we did two searches:

1. **Similarity of meaning.** A local model (Nomic Embed Text v1.5) changed award descriptions into vectors
   of numbers. Awards with a similar meaning have similar vectors. We wrote 20 search questions about AI
   work. We found the awards most similar to each question. Language-model agents examined the recipients.
2. **Project identifiers.** Small business research programmes (SBIR and STTR) and agencies publish lists
   of AI projects with their identifiers. We found the same identifiers in the USAspending data. This route
   found AI awards that have a very general description in USAspending.

We did these searches two times:

| Pass | Input | Recipients added |
|---|---|---|
| First pass | 15,000 sample awards, 100 project leads | 11 |
| Second pass | 20,000 sample awards, 211 project leads | 40 |

The search by project identifiers added the most recipients for each review.

## 9. Result of Part A

The list has 2,009 recipients. The list contains 14,376 different awards that we selected as AI awards.
A keyword search selected 14,014 of these awards. No person or model read these awards one by one.
Language-model agents approved 365 awards one by one, with the rule of section 7. (Three awards are in
both groups. Some awards have more than one recipient record. Thus, the map in Part B shows 14,601 records
for these 14,376 awards.)

For the money of each selected award, we added all FY2026 transactions. We included negative transactions.
We removed duplicate transactions. The total value of an award is not always the value of its AI part.

# Part B: a decision for each award

## 10. Why we examined each award

Part A decided for each recipient. But a recipient can have AI awards and other awards. Also, no person or
model read most of the awards in the list. Thus, in Part B, we made one decision for each award.

## 11. The map of all awards

We put all 5,189,400 records on a map. On this map, awards with similar descriptions are near each other.

1. We changed each description into a vector with the model BAAI/bge-base-en-v1.5. The model reads the
   first 256 tokens of each text. A token is approximately one word or one part of a word.
2. For each award, we calculated the mean of the vectors of all its descriptions.
3. We decreased the vectors to 64 dimensions with principal component analysis (PCA).
4. We decreased the vectors to 2 dimensions with UMAP. This gives the position of each award on the map.

We divided the awards into 1,500 small topics with k-means clustering. We put these topics into a tree of
larger topics. Each larger topic contains a maximum of 8 smaller topics. We found the typical words of each
topic with c-TF-IDF. A local language model (Qwen 3.5, 9B) wrote a short name for each topic. The names help
the reader. The typical words are the evidence.

## 12. The awards that we examined

We did not examine all awards. We selected the awards that can possibly be related to AI:

1. For each award, we found the 5 nearest awards in the list of Part A. We calculated the mean cosine
   similarity of the vectors. This is the "AI similarity" of the award.
2. We kept the 500,000 awards with the highest AI similarity.
3. We added all awards that contain an AI word. There are 99,657 of these awards.
4. We added all awards from the list of Part A.

This gives 611,344 awards. We call these awards "in scope".

We tested this selection before we used it. We removed some AI awards from the reference list. Then we did
the selection again. The 500,000 nearest awards contained 99.8% of the removed AI awards.

## 13. The decision for each award

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

Before the full run, we did these tests:

1. We gave Jev 100 awards that were approved one by one in Part A. Jev identified 93 of them as
   AI-related. Jev identified most of the other 7 as "Unclear". These 7 awards are mostly administrative
   changes with no description of the work.
2. We gave Jev 400 random awards in scope. Jev identified 89% as not AI, 9% as unclear, and 2% as
   AI-related. We read the AI-related awards. Most of them were correct.

We also tested a local model (GLiNER2.5-Decide) with the same type of question. It identified only 63% of the
approved AI awards. Thus, we did not use it.

## 14. Tests of the awards that we did not examine

We did not examine 4,578,056 records. We did these tests to find if we missed AI awards:

1. We took 1,000 random awards from each of three groups: the 50,000 awards immediately below the limit of
   AI similarity, the next 450,000 awards, and all other awards. Jev identified 2 of the 3,000 awards as
   AI-related. We read the 2 awards. Neither award is related to AI.
2. We took 20,000 random awards from all the awards that we did not examine. Jev identified 2 awards as
   AI-related. We read the 2 awards. Neither award is related to AI.

We found no AI award in 20,000 random awards. Thus, with 95% confidence, less than 0.015% of the awards that
we did not examine are related to AI. This is a maximum of approximately 700 awards.

## 15. Results of Part B

Each record has one of these statuses:

| Status | Records |
|---|---|
| AI-related, recipient not in the list of Part A (new lead) | 1,847 |
| AI-related, recipient in the list, award not in the list | 6,007 |
| AI-related, award in the list | 10,147 |
| In the list, but Jev did not identify it as AI-related | 4,454 |
| Unclear | 67,563 |
| Not AI | 521,326 |
| Not examined | 4,578,056 |

In total, 18,001 records are AI-related.

## 16. Limits

1. Jev reads only the description. It does not read the amount, the recipient, or other sources.
2. Many awards are administrative changes with a very short description. Jev cannot decide these awards.
   They have the status "Unclear".
3. A person must examine the 4,454 records that Jev did not confirm. Some of these awards are possibly AI
   awards with a short description.
4. A person must examine a random sample of the 1,847 new leads. This sample will show how frequently Jev is
   correct about new awards.
5. The data does not include all subcontracts, all subgrants, or the members of consortia that get "other
   transaction" agreements. Defense data from July to September 2026 is possibly not complete yet.
6. The positions on the map are correct only for near awards. A large distance between two groups on the
   map does not have a clear meaning.
