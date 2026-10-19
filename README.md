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

This project uses public stock market discussions from Stocktwits to build a text classifier. The goal is to identify different types of stock discussions based on how investors present evidence, predictions, emotional reactions, and trading activities.

I chose Stocktwits because I am interested in investing and want to understand how people communicate about stocks. I am collecting and labeling real posts to fine-tune a model that can classify new stock market discussions.

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

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- _What I asked for:_
- _What came back:_
- _What I changed:_

**Moment 2**

- _What I asked for:_
- _What came back:_
- _What I changed:_

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure          | Baseline | Trained | Difference |
| ---------------- | -------- | ------- | ---------- |
| Overall accuracy |          |         |            |
| Macro F1         |          |         |            |
| F1 — `label_one` |          |         |            |
| F1 — `label_two` |          |         |            |

**What I predicted before I looked:**

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**

<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

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

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   |           |        |         |               |
| 2   |           |        |         |               |
| 3   |           |        |         |               |
| 4   |           |        |         |               |
| 5   |           |        |         |               |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** **_ / 30 = _**%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

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
