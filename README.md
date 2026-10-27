# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

My project is called TakeMeter. I built an AI classifier that reads public Stocktwits posts and categorizes them into four groups:

analysis: Posts that use financial data or technical evidence.

prediction: Posts predicting stock prices or market movements without supporting evidence.

reaction: Posts expressing emotions, excitement, frustration, or humor.

trade_update: Posts reporting trades, positions, or investment results.

I collected 200 Stocktwits posts, mainly discussing NVDA, and used them to fine-tune a pretrained DistilBERT model.

The goal is to help distinguish useful financial discussions from speculation, emotional reactions, and personal trading updates.

My first model achieved 56.7% test accuracy. It performed better on analysis and reaction posts but struggled with prediction and trade_update. I plan to investigate these weaknesses in Unit 6.

---

## Label Taxonomy

### Milestone 1 — Community Observations

**Community:** Stocktwits

**Posts reviewed:** [50]

**Date:** October 14, 2026

**Three differences I observed:**

1. Some investors explain their opinions using financial numbers, earnings estimates, or economic events, while others make predictions without explaining why.

2. Some investors provide specific stock price targets and expected timelines, while others express only their feelings about price movements.

3. Some posts describe actual trading activities, such as buying shares or closing options, while others focus on opinions, predictions, or reactions to market news.

**Why these differences matter:**

These differences could help train a classifier to recognize whether a stock discussion contains analysis, a prediction, a market reaction, or a trading update.

### `analysis`

**Definition:**A post that uses specific, checkable financial, economic, or technical information to explain a market opinion or frame a market question.

**Example 1:Finwizard61**

> $NVDA Now officially trading at an absurdly low future multiples …

1. at 23 x 2027 eps estimate…
2. at 14 x 2028 eps estimate..

Sell off unwarranted …load up folks this is a gift 🎁 at these low prices..

**Example 2:tpanikorn:**

> @superrush That is right. Timing is everything in business. $450M cash in the bank, growing $28M last quarter. QNX backlog just crossed $1B while revenue grew 26%. The Alloy Kore design win added over $100M into backlog. There will be at least one more contract before year end. This is an easy sale for underwriters. They have just to mention $NVDA Drive OS and Physical AI platform using QNX. It is a no brainer.

### `prediction`

**Definition:**A post that predicts or speculates about future market movement without specific, relevant supporting evidence.

**Example 1:**

> $NVDA wow this is going to 150 next month

**Example 2:**

> NVDA 230 next week stop being scared load the boat

Why: Both posts predict a future price without providing evidence supporting the prediction.

### `reaction`

**Definition**: A post that mainly expresses emotions, excitement, frustration, or humor about the market without providing analysis or a specific prediction.

**Example 1** — DrScalp:

$NVDA this is brutal

**Example 2** — Tommojg:

$NVDA mother of god

Why: These posts mainly express emotions rather than explaining financial information or predicting a specific market movement.

### 'trade_update'

**Definition**: A post that reports a completed trade, an existing investment position, or a personal trading result.

**Example 1** — Shyne123:

$NVDA I made off like a bandit on my puts last week and this week. I’m off to Vegas 🍻

**Example 2** — ShadowCapital\_:

$NVDA Making money on both sides of this dog today. Shorts this morning, and caught that pop into close.

Why: These posts describe personal trading activity and results rather than simply expressing an opinion or prediction.

### The hardest boundary

**Which two labels:**analysis and prediction

**Why this boundary is difficult**:

Both types of posts can discuss future stock prices. Some investors mention specific price targets, but that doesn't necessarily mean they have provided evidence supporting their predictions.

**The decision rule I used every time:**

If a post makes a prediction and explains it using specific, checkable supporting information, I classify it as analysis. If it only predicts a price or market direction without specific supporting evidence, I classify it as prediction. A price target by itself does not count as supporting evidence.

**Additional rule for overlapping categories:**

If a post explicitly reports a completed trade or existing position, I classify it as trade_update when that is its main purpose. If the post mainly expresses emotion without a supported argument, prediction, or trading update, I classify it as reaction.

**Optional Stretch Feature — Fourth Label**

I chose trade_update as a fourth label because the other three categories do not specifically capture investors reporting trades they completed, positions they hold, or gains and losses they experienced. These posts appear in the Stocktwits community and represent a distinct communication pattern.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

I collected 200 public posts from Stocktwits, primarily from discussions mentioning NVDA. I kept the full post text in one labels.csv file without manually splitting the dataset.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
I labeled the first 20 posts independently without AI assistance and marked them cold. I used AI to suggest labels for the remaining 180 posts. After a second AI review, I personally reviewed and approved all 180 classifications. Those rows are marked ai_assisted_reviewed.

**Counts per label:**

| Label        |   Count |    Share |
| ------------ | ------: | -------: |
| reaction     |      70 |      35% |
| analysis     |      55 |    27.5% |
| prediction   |      53 |    26.5% |
| trade_update |      22 |      11% |
| **Total**    | **200** | **100%** |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.Analysis vs. Prediction**

> _The post:_
> $NVDA Nvidia slices through its 50-day moving average. Can it recover it by the end of the week? #ibdpartner @MarketSurge @InvestorsBusinessDaily
> _Could have been:_
> analysis or prediction.
> _I chose, because:_

analysis because the post refers to a specific technical indicator, the 50-day moving average. Although it asks about a future recovery, it does not make a direct price forecast.

**2.Trade Update vs. Reaction**

> _The post:_
> $NVDA 222.5p +330%🔥
> _Could have been:_
> trade*update or reaction.
> \_I chose, because:*
> trade_update because the options contract and percentage gain suggest a personal trading result, even though the fire emoji expresses excitement.

**3.Prediction vs. Reaction**

> _The post:_
> $NVDA 225? lol 125 was already long ago!! Tho won’t be there anytime soon!!
> _Could have been:_
> prediction or reaction.
> _I chose, because:_

## prediction because the writer is expressing an expectation about the stock's future price, despite the humorous language.

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**
distilbert-base-uncased (DistilBERT)

**Settings:** <!-- epochs, learning rate, batch size, seed -->
Epochs: 3

Learning rate: 2e-5

Batch size: 16

Random seed: 42

Maximum sequence length: 128 tokens

Device: CP

**Anything I changed from the defaults, and why:**
I kept all the default training settings provided by CodePath. I changed the dataset path from data/practice_labels.csv to labels.csv to train on my 200 Stocktwits posts. I also configured the four labels: analysis, prediction, reaction, and trade_update.

**Split sizes:**
Training: 139 posts (69.5%)

Validation: 31 posts (15.5%)

Test: 30 posts (15%)

**Dataset Split (Seed 42)**

| Label          | Training (70%) | Validation (15%) | Test (15%) |   Total |
| :------------- | -------------: | ---------------: | ---------: | ------: |
| `analysis`     |             38 |                9 |          8 |      55 |
| `prediction`   |             37 |                8 |          8 |      53 |
| `reaction`     |             48 |               11 |         11 |      70 |
| `trade_update` |             16 |                3 |          3 |      22 |
| **Total**      |        **139** |           **31** |     **30** | **200** |

The dataset contains 200 labeled Stocktwits posts, split into 139 training examples, 31 validation examples, and 30 test examples.

The `trade_update` category has only 3 test examples, making its F1 score less reliable. Even one incorrect prediction can significantly affect the results. I will investigate this limitation when evaluating the model across three different seeds in Unit 6.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

What I asked for: I asked Ai to help me figure out why my notebook was showing errors when checking my dataset.

What came back: Ai helped me identify that the notebook was loading the 60-example practice dataset instead of my 200 Stocktwits posts.

What I changed: I changed the CSV path from data/practice_labels.csv to labels.csv. After running the validation again, my dataset passed all the checks.

**Moment 2**
What I asked for: I asked Ai to help me understand and create measurable acceptance criteria for my classifier.

What came back: Ai explained accuracy, F1 scores, dataset balance, and consistency. It also helped me compare different numerical targets.

What I changed: I decided to use a 55% maximum label share instead of the initially suggested 45% because I wanted to allow some natural differences between Stocktwits categories. I also chose my own accuracy and F1 targets.

**Pre-labelling disclosure:**
I manually labeled my first 20 Stocktwits posts without AI assistance. For the remaining 180 posts, I used AI to suggest labels based on my four categories: analysis, prediction, reaction, and trade_update.

I also asked AI to review the suggestions and correct potential mistakes. Afterward, I personally reviewed and approved all 180 labels before training my model. The AI-assisted rows are marked ai_assisted_reviewed in my CSV.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

Both models were tested on the same 30 held-out stock-market posts using my four labels: analysis, prediction, reaction, and trade_update.

| Measure             | Baseline | Trained | Difference |
| ------------------- | -------- | ------- | ---------- |
| Overall accuracy    | 0.333    | 0.567   | +0.233     |
| Macro F1            | 0.371    | 0.362   | -0.009     |
| F1 — `analysis`     | 0.222    | 0.800   | +0.578     |
| F1 — `prediction`   | 0.471    | 0.000   | -0.471     |
| F1 — `reaction`     | 0.125    | 0.647   | +0.522     |
| F1 — `trade_update` | 0.667    | 0.000   | -0.667     |

**What I predicted before I looked:**

My initial expectation would have been that the fine-tuned model would outperform the baseline because it was trained on my specific stock-market classification rules. I would particularly have expected improvements in analysis and trade_update, since financial evidence and completed trading actions provide recognizable features for classification.

**What the gap actually means:**

My fine-tuned model performed better than the baseline in overall accuracy, improving from 33.3% to 56.7%. This means it correctly classified 7 more posts out of the 30 tested.

However, the results also show a problem. My model improved significantly on analysis and reaction, but scored 0.000 F1 on prediction and trade_update. The macro F1 decreased slightly from 0.371 to 0.362.

This tells me that fine-tuning helped my model recognize some categories, but it still struggles with others. One possible reason is that some of my labels overlap or were applied inconsistently during training.

---

## Run Log — Before

| Criterion                   | Target             | Seed 42       | Seed 7        | Seed 2024     | Verdict            |
| --------------------------- | ------------------ | ------------- | ------------- | ------------- | ------------------ |
| 1. Overall accuracy         | >= 0.70 each seed  | 0.567         | 0.567         | 0.433         | MISSED             |
| 2. Every label F1           | >= 0.60 each label | Min F1: 0.000 | Min F1: 0.000 | Min F1: 0.000 | MISSED             |
| 3. Dataset balance          | No label > 55%     | 35% max       | 35% max       | 35% max       | MET                |
| 4. Consistency across seeds | Spread <= 0.15     | 0.567         | 0.567         | 0.433         | MET (spread 0.133) |
| 5. Labeling agreement       | >= 16/20           | Not tested    | Not tested    | Not tested    | PENDING            |

**Criterion 3 — Dataset Balance: MET**

My dataset contains 200 labeled Stocktwits posts: 70 reaction (35%), 55 analysis (27.5%), 53 prediction (26.5%), and 22 trade_update (11%).

The largest category is reaction at 35%, which is below my original 55% limit. Therefore, this criterion is MET.

However, trade_update contains significantly fewer examples than the other categories, which might contribute to its poor classification performance. I will investigate this further before deciding on an improvement.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |     |     |     |
| ---------------- | --- | --- | --- |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |

**My biggest off-diagonal number, and what it means:**

<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion               | Target                                  | Verdict | How I decided                                                                                                                          |
| --- | ----------------------- | --------------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Overall accuracy        | >= 70% on all three seeds               | MISSED  | Accuracy was 56.7%, 56.7%, and 43.3%. None reached 70%.                                                                                |
| 2   | Per-label F1            | >= 0.60 for every label                 | MISSED  | Prediction and trade_update scored 0.000 F1 across all three seeds.                                                                    |
| 3   | Dataset balance         | No label above 55%                      | MET     | Reaction was the largest category at 35%, below 55%.                                                                                   |
| 4   | Training consistency    | Accuracy spread <= 15 percentage points | MET     | The accuracy spread was 13.3 percentage points.                                                                                        |
| 5   | Self-labeling agreement | At least 16/20 (80%)                    | MET     | I independently labeled 20 Stocktwits posts twice, at least 24 hours apart. My labels matched on 16 of 20 posts, giving 80% agreement. |

**Diagnoses**
Criterion 1 — Overall Accuracy: MISSED

My model did not achieve 70% accuracy on any of the three seeds. The confusion matrix shows that many mistakes came from classifying prediction and trade_update posts as reaction.

Criterion 2 — Per-Label F1: MISSED

Both prediction and trade_update scored 0.000 F1 across all three seeds. In Seed 42, the model predicted only analysis and reaction, completely ignoring the other two categories.

One possible explanation is the imbalance in my training data. I have 70 reaction examples but only 22 trade_update examples. Another possibility is that the model did not learn clear boundaries between the categories.

My 30-post staff agreement test used a different taxonomy and does not establish whether my original Stocktwits labels were consistent. I would need to review those original labels to confirm whether inconsistent labeling contributed to the failures.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** **30 / 30 = 100%**

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

The comparison found no disagreements between my submitted labels and the staff labels, so there are no disagreements to adjudicate or sort into the three piles.

What the pattern in my disagreements tells me:

All 30 submitted labels matched the staff classifications. The posts were organized into fairly recognizable examples of analysis, unsupported opinions, and immediate reactions.

This agreement result also does not prove that my original four Stocktwits labels were consistently applied. My fine-tuned model still struggled with prediction and trade_update, which requires further investigation.

**1.**

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**2.**

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**What the pattern in my disagreements tells me:**
All 30 submitted labels matched the staff classifications. The examples appeared relatively straightforward to categorize because the distinctions between analysis, hot takes, and reactions were recognizable.

Since there were no disagreements, there were none to adjudicate into the three piles. This result does not establish whether my original four Stocktwits labels were consistently applied.

---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

**The gap between what I meant my labels to capture and what the model
learned:**

<!-- Two sentences. Your confusion matrix is the evidence. -->

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
