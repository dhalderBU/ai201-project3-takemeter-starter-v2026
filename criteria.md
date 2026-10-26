# Acceptance criteria — TakeMeter

Seven proposed acceptance criteria for the four-label NeutralPolitics classifier.
The original exercise requests five; this draft retains the seven current criteria.
These are targets to evaluate, not claims about achieved model performance.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Uniform random guessing across these four
labels has an expected accuracy of 25%, while always predicting Executive
Branch achieves 97/200, or 48.5%, on the current dataset. A useful accuracy
target must meaningfully exceed that majority-label baseline.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

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

## Dataset and evaluation setup

Source: [reditt_neutral_politics_labled_sorted.csv](data/reditt_neutral_politics_labled_sorted.csv).
Only `text` and `label` inform these criteria. The classifier receives `text`
as its input and learns to predict `label`; the true label is used for training
and evaluation, never as an inference input. The `note` column is personal
commentary about potential classification ambiguity and is excluded from training data
and criteria.

The verified counts are **97 Executive Branch, 39 Legal & Investigations,
33 Foreign Policy, and 31 Elections**, totaling **200 labeled posts**.
Trim label whitespace and map `Legal and investigations` to
`Legal & Investigations` before counting or splitting so there are exactly
four categories.

| Label | Posts | Share | Verified test posts per seed |
|---|---:|---:|---:|
| Executive Branch | 97 | 48.5% | 14 |
| Legal & Investigations | 39 | 19.5% | 6 |
| Foreign Policy | 33 | 16.5% | 5 |
| Elections | 31 | 15.5% | 5 |
| **Total** | **200** | **100.0%** | **30** |

Use the notebook's stratified 70/15/15 train/validation/test split and seeds
**42, 7, and 2024**. Shuffle through the stratified split; do not take contiguous
blocks from this category-sorted file. Record actual per-label support for
each seed. With these 200 labels, the current notebook's split function
produces **139 training, 31 validation, and 30 test posts**. 

Criteria 1–6 must pass on the held-out test set for **every seed**;
averaging across seeds does not turn a miss into a pass. Criterion 7 compares
all three runs. Freeze the dataset, label decision rules,
and targets before evaluating; use validation data for model selection.
Treat undefined metrics as not evaluable, not as passes.

The header is `text,label,note`, and all **200 posts have labels**. One record
still has an additional empty field; handle that formatting issue in data
preparation while preserving its text and label. Only those two fields were
used to verify the counts and splits. The CSV is not changed by this update.

---

## 1. Overall accuracy of at least 65%

At least **65%** of held-out posts must receive their correct label in each
seed. With 30 test posts, that means at least **20 correct predictions**
(20/30 = 66.67%; 19/30 = 63.33% falls below the target).

**Why this target:** Executive Branch accounts for 97 of 200 posts, giving
a full-dataset majority-label baseline of 48.5%. The verified test split
contains 14 Executive posts out of 30, so its majority-label baseline is
approximately 46.67%; the 65% target requires six more correct predictions.

---

## 2. Macro F1 of at least 0.65

The unweighted mean of the **four per-label F1 scores must be at least 0.65**
in each seed. Include all four labels, assigning F1 of zero when a class has
no successful predictions.

**Why this target:** Executive Branch has 97/31, or approximately 3.13 times as many
examples as Elections. Macro F1 gives each category equal weight, and a
0.65 target requires useful performance beyond the largest category.

---

## 3. Minimum F1 for every label

Every label must meet its own F1 threshold in each seed:

| Label | Minimum F1 |
|---|---:|
| Executive Branch | 0.70 |
| Legal & Investigations | 0.60 |
| Foreign Policy | 0.60 |
| Elections | 0.55 |

**Why this target:** Executive Branch has 97 examples and 14 test posts,
supporting a higher target. Legal's 39 examples yield six test posts,
Foreign Policy's 33 yield five, and Elections' 31 also yield five. The smaller
categories retain their lower targets to allow for greater variation, with
Elections still the smallest category in the full dataset.

---

## 4. Recall of at least 60% across the three smaller categories

Among all test posts truly labeled **Legal & Investigations, Elections, or
Foreign Policy, at least 60% must receive their exact correct label** in each
seed. Divide correct predictions within these three categories by the total
number of true posts in those categories. This is pooled recall over the
three labels, not a confidence metric. Predicting the wrong smaller category
still counts as an error.

**Why this target:** These categories together contain **39 + 31 + 33 = 103
posts**, compared with 97 Executive Branch posts. Their test counts total
**6 + 5 + 5 = 16**, so 60% requires **at least ten correct predictions**
(10/16 = 62.5%; 9/16 = 56.25% falls below the target).
This checks that overall accuracy is not carried mainly by Executive Branch.

---

## 5. Accuracy spread across seeds no greater than 10 percentage points

The **highest test accuracy minus the lowest test accuracy across seeds 42,
7, and 2024 must be no greater than 0.15** for Executive branch, and 0.25 for the other three categories(i.e. Elections, Foreign Policy, Legal and Investigations) as they have less number of rows.


**Why this target:** Each current test set contains only 30 posts, so one
prediction changes accuracy by about 3.3 percentage points. A 10-point limit
allows a difference equivalent to three correct predictions while rejecting
larger swings caused by which small-category posts enter the split. So depending on how the split is done we may have slightly inferior perfromance for label calss other that Executuve Branch(executing branch has 98 rows out of 200 and we have four labels and others are in 30 to 40 count range) 

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
