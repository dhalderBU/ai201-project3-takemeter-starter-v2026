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

This project classifies public posts from Reddit's r/NeutralPolitics community by their main political topic. It assigns one of four labels: Executive Branch, Legal and Investigations, Foreign Policy, or Elections. I selected 200 posts from a Kaggle Reddit dataset, used Claude to assist with classification, and manually reviewed and corrected the labels. The project tests whether a classifier can distinguish these topics, especially when executive officials are involved in legal or investigative questions.

---

## Label Taxonomy

The label names below match `data/reditt_labels.csv` after trimming surrounding whitespace; the CSV spells the legal category `Legal and investigations`. All examples are quoted directly from the file's `text` column. Record numbers count data records, excluding the header.

I plan to use four categories.

### `Executive Branch`

**Definition:** Posts primarily about presidential or executive agency powers, appointments, administration, policy actions, presidential wrongdoing, presidential statements, presidential actions including justice department and FBI.

**Example 1:** (record 10)

> Can the President forgive student debt?

**Example 2:** (record 71)

> Is the pace of President Trump's Cabinet appointments similar to those of previous administrations?

### `Legal and investigations`

**Definition:** Posts primarily about courts, investigations, alleged wrongdoing, legal proceedings, or whether a specific action violates the law.

**Example 1:** (record 121)

> Robert Mueller has been appointed a special counsel for the Russia probe. What is that and how does it work?

**Example 2:** (record 127)

> What is the legal interpretation of the SCOTUS' refusal to grant cert to an appeal?

### `Foreign Policy`

**Definition:** Posts primarily about international relations, diplomacy, sanctions, treaties, military conflict, or relations between countries.

**Example 1:** (record 148)

> What are the implications of the US backing out of UNESCO?

**Example 2:** (record 153)

> What incentives and new sanctions does Iran face given US withdrawal from the 'Iran Deal?'

### `Elections`

**Definition:** Posts primarily about voting, campaigns, candidates, polling, election results, or electoral systems and procedures.

**Example 1:** (record 175)

> What are the advantages and disadvantages of unconditionally allowing people to vote by mail?

**Example 2:** (record 189)

> What are the arguments for and against gerrymandering?

### The hardest boundary

**Which two labels:** `Executive Branch` and `Legal and investigations`.

**The decision rule:** Classify the main question being asked. General questions about executive authority, appointments, administration, or policy belong in `Executive Branch`. Questions centered on an investigation, court proceeding, alleged wrongdoing, or a specific violation of law belong in `Legal and investigations`, even when they name the president, the Justice Department, or the FBI. A person's name or an agency's place in government does not decide the label by itself.

**Consistency limit:** This is the intended boundary rule. The current labels do not apply it perfectly: the Jeff Sessions perjury question in record 1 is labeled `Executive Branch`, although its central question concerns alleged wrongdoing. This is a labeling inconsistency to review separately from the model's prediction errors.

In my manual review, Elections and Foreign Policy generally felt clearer to categorize. I expect confusion in both directions between Executive Branch and Legal and Investigations because their subjects overlap.

---

## The Dataset

**Where the posts came from:** Public r/NeutralPolitics posts obtained through a Kaggle Reddit dataset. The selected 200-record dataset used for the examples and counts here is [reditt_labels.csv](data/reditt_labels.csv).

**How I labelled them:** I selected the four topic categories and assigned labels, also had Claude classify the posts, and then manually reviewed and corrected the labels. This was an AI-assisted labeling process with human review.

The file contains three columns:

- **`text`:** the Reddit text to be provided to the classifier.
- **`label`:** the target category used for training and evaluation.
- **`note`:** my observations about ambiguous records and possible misclassification. These notes document my review and are excluded from classification inputs and classification criteria.

**Counts per label:** Counts use the `label` column with surrounding whitespace removed.

| Label | Count | Share |
|---|---:|---:|
| Executive Branch | 97 | 48.5% |
| Legal and investigations | 39 | 19.5% |
| Foreign Policy | 33 | 16.5% |
| Elections | 31 | 15.5% |
| **Total** | **200** | **100.0%** |

Executive Branch represents nearly half the dataset, so I expect the additional examples may help the model learn that category. Legal and Investigations accounts for 19.5%, while Elections is the smallest category at 15.5%. With only 200 posts across four classes, the smaller categories have limited examples in each split, and performance may vary with the split and random seed. These are expectations to test with per-class metrics and a confusion matrix.

**Three hard cases**

All three posts have misclassification notes in [reditt_labels.csv](data/reditt_labels.csv). The first two are labeled `Executive Branch`, and the third is labeled `Legal and investigations`. The explanations below use the reasoning recorded in those notes.

**1. Jeff Sessions and the Russian Ambassador — record 1**

> *The post:* Did AG Jeff Sessions commit perjury during his confirmation hearing when he didn’t disclose prior meetings with the Russian Ambassador?
>
> *Could have been:* `Executive Branch` or `Foreign Policy`.
>
> *I chose, because:* `Executive Branch`. I kept the label for the question about the Attorney General's conduct during his confirmation hearing. My note flags the reference to another country: mentioning the Russian Ambassador could cause a classifier to select Foreign Policy.

**2. The Fed injecting money into the economy — record 31**

> *The post:* How Can the Fed Inject $1.5 Trillion Into the Economy?
>
> *Could have been:* `Executive Branch` or `Economy`, if Economy is added as a future category. Economy is not one of the current four labels.
>
> *I chose, because:* `Executive Branch`. My note groups the Fed's action under this category and flags the economic subject matter as a possible source of confusion if an Economy label is added. This describes the category assigned in my dataset and a potential future boundary.

**3. Donald Trump Jr.'s emails — record 98**

> *The post:* Do the recently released emails relating to Donald Trump Jr. indicate any criminal wrongdoing?
>
> *Could have been:* `Legal and investigations` or `Executive Branch`.
>
> *I chose, because:* `Legal and investigations`. My note warns that the Trump name could make the classifier select Executive Branch. The question is about whether the emails indicate criminal wrongdoing, so I chose Legal and Investigations.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** 

The code uses distilbert-base-uncased, a pretrained DistilBERT model, and fine-tuned it to classify

**Settings:** <!-- epochs, learning rate, batch size, seed -->

EPOCHS = 3
LEARNING_RATE = 2e-5
BATCH_SIZE = 16

**Anything I changed from the defaults, and why:**

I didnt change it as a part of Unit 5 though I did test few beyind this as defualt is not giving me good result but I will show the results in Unit 6

**Split sizes:** 139 training / 31 validation / 30 test posts, using a stratified split targeting approximately 70% / 15% / 15%.

| Label | Train | Validation | Test |
|---|---:|---:|---:|
| Executive Branch | 68 | 15 | 14 |
| Legal and Investigations | 27 | 6 | 6 |
| Foreign Policy | 23 | 5 | 5 |
| Elections | 21 | 5 | 5 |
| **Total** | **139** | **31** | **30** |

Legal and Investigations, Foreign Policy, and Elections each have **fewer than eight test examples**. With these small groups, even one additional misclassification can substantially change per-class metrics, contributing to variation across random seeds.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

I used Claude to write a code to initial classification
I used calude to get ideas about how key words so as I can understand the data better
How to connect to Reditt but it didnt roek

**Moment 1**

- *What I asked for:*
https://www.reddit.com/r/politics/  and get me 200  psts and generate 200 posts and label. Label is the class /category of the post. I am trying to use it for classification
- *What came back:*
Reddit blocked my attempt to open r/politics, so I can’t pull real posts from it. Even without the block, a single page only shows about 25 posts, so getting 200 would need Reddit’s API or a dataset anyway.

There are two ways to get your 200 labeled posts:

Real posts, which is better for training a classifier. I can write you a short Python script using Reddit’s official API (PRAW) that collects 200+ posts from r/politics (title, score, flair, URL, date). You’d run it with free API credentials from reddit.com/prefs/apps. Reddit’s own post flairs could serve as rough labels, and I can add a keyword or LLM-based labeling step on top. Public datasets on Kaggle or Hugging Face are another option if you don’t want to use the API.
Synthetic posts. I can generate 200 realistic r/politics-style headlines, each with a label, as a CSV file. The categories could be Elections, Congress/Legislation, Judiciary/Courts, Foreign Policy, Economy, Immigration, Healthcare, Climate/Environment, Executive Branch, and Civil Rights. The text would be made up rather than real posts, so it’s fine for prototyping a pipeline but weaker for a model you want to generalize to real Reddit data.

Which one do you want? If you choose the synthetic route, tell me if you want different categories or a different number per category, otherwise I’ll balance them evenly (20 per class).
- *What I changed:*

**Moment 2**

- *What I asked for:*
give me a pythocode for that
- *What came back:*
I wrote the script, collect_politics_posts.py. I haven’t run it, because I can’t reach Reddit from here.

To run it:

Run pip install praw pandas.
Create a “script” app at reddit.com/prefs/apps and copy the client ID and secret.
Set the environment variables REDDIT_CLIENT_ID, REDDIT_CLIENT_SECRET and REDDIT_USER_AGENT.
Run python collect_politics_posts.py.

What it does:

It pulls posts from the hot, top (past month), new and rising listings, removes duplicates and pinned posts, and stops at 200.
It labels each title with keyword rules into 10 categories (Elections, Judiciary, Congress, Foreign Policy, Economy, Immigration, Healthcare, Climate/Environment, Civil Rights, Executive Branch). Titles that match nothing get “Other”.
It saves politics_posts_labeled.csv with the title, label, Reddit flair, score, comment count, timestamp and URL, and prints the label counts.

Keep in mind:

Keyword labels are noisy. A title like “Trump signs order on border wall” matches whichever rule comes first, and one post can fit several categories. Spot-check a sample, or use the labels as a first pass and fix them by hand.
If “Other” gets too large or one class dominates, edit the CATEGORY_KEYWORDS dictionary. For a cleaner dataset, you can replace keyword_label with an LLM call that classifies each title.
If you train and test on labels made by keyword rules, the model mostly learns those rules back. Hand-correct at least your test set.

I can add an LLM-based labeling step or a train/test split with a baseline classifier (TF-IDF plus logistic regression) if you want either.

I then got psuedo code
- *What I changed:*

**Pre-labelling disclosure:**

I used claude ideas and then manually reviewed and changed labels

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

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
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

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

**Agreement rate:** ___ / 30 = ___%

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
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

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

       [ X] criteria.md has five numbered criteria, each naming a NUMBER
       [ X] Each has a reason underneath tied to your data or taxonomy
       [ X] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ X] No label above 70%
       [ X] All five unit 5 sections have real content
       [X ] Label Taxonomy includes the decision rule for your hardest boundary
       [X ] The Dataset includes three hard cases
       [X ] results.json and test_split.csv committed (the notebook does this)
       [ X] At least four new commits
       [X ] Repository URL submitted — WRITE IT DOWN

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
