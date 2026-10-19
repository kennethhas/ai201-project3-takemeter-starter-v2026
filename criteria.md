# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. _"The model is accurate"_ is an
opinion. _"Every label has an F1 of at least 0.60 on the held-out set"_ is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — _"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"_. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area                  | A question it could answer                                            |
| --------------------- | --------------------------------------------------------------------- |
| Overall accuracy      | How often does it need to be right to be worth using?                 |
| Per-label performance | Is one label allowed to be much worse than the others?                |
| Balance               | How lopsided can your label counts get before it's a problem?         |
| Consistency           | If someone else labelled the same posts, how often should you agree?  |
| Confidence            | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1.Overall Accuracy

My Stocktwits classifier must achieve at least 70% accuracy on the held-out test set for each of three training seeds (42, 7, and 2024).

How to measure: Run the model with each seed and calculate the percentage of correctly classified posts in the test set. All three runs must reach at least 70%.

**Why this target:**

I chose 70% because I want my AI to correctly categorize most stock discussions. My four labels are analysis, prediction, reaction, and trade_update. Some posts are difficult to distinguish, especially analysis and prediction, so I think 70% is a challenging but reasonable target for a small dataset.

---

## 2.Per-Label F1 Score

Per-Label F1 Score

Each of my four labels (analysis, prediction, reaction, and trade_update) must achieve an F1 score of at least 0.60 on the held-out test set for each of the three training seeds.

How to measure: Calculate precision and recall for each label, then calculate its F1 score. Every label must achieve at least 0.60 in all three runs.

**Why this target:**

I chose 0.60 because I don't want my AI to perform well on only one or two categories while ignoring the others. For example, the model might recognize emotional reactions easily but struggle with trade updates or predictions. I want the model to have reasonable precision and recall across all four labels.

---

## 3.Dataset Balance

No single label should represent more than 55% of the total labeled posts in labels.csv.

How to measure: Count how many posts belong to each label and divide each count by the total number of labeled posts. Every label must represent 55% or less of the dataset.

**Why this target:**

I chose 55% because some types of discussions naturally appear more often on Stocktwits than others. I want to allow this difference without letting one category dominate the dataset. This should give the model a better opportunity to learn analysis, predictions, reactions, and trade updates instead of focusing mostly on one category.

---

## 4. Consistency Across Training Runs

The difference between the highest and lowest overall test accuracy across three training seeds (42, 7, and 2024) must be 15 percentage points or less.

How to measure: Train and evaluate the model using the same dataset and settings with three different seeds. Record the test accuracy for each run, then subtract the lowest accuracy from the highest.

**Why this target:**

I chose 15 percentage points because my Stocktwits dataset is relatively small, and changing the random seed can affect which posts are used for training and testing. I expect some differences between runs, but I don't want the model's performance to change too dramatically depending on the seed.

---

## 5.Labeling Agreement

When I independently label the same 20 Stocktwits posts twice, my labels must agree on at least 80% of the posts (16 out of 20).

How to measure: First, manually classify 20 posts without AI assistance and record the results. At least 24 hours later, classify those same posts again without looking at the original labels. Compare both sets and count how many posts received the same label.

**Why this target:**

I chose 80% because I want to make sure my label definitions are clear and consistent. While reviewing Stocktwits posts, I noticed that some could fit more than one category, especially analysis versus prediction and reaction versus trade_update. If I cannot classify the same posts consistently, the AI may also have trouble learning the differences.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
