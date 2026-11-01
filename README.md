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

I have created my own datset and not used the dataset provided in the assignments (i.e. noteven augmented that dataset)

In this project I am classifing public posts from Reddit's r/NeutralPolitics community by their main political topic. It assigns one of four labels: Executive Branch, Legal and investigations, Foreign Policy, or Elections. I selected 200 posts from a Kaggle Reddit dataset, used Claude to assist with classification, and manually reviewed and corrected the labels as claude did not classify properly. The project tests whether a classifier can distinguish these topics, especially when executive officials are involved in legal or investigative questions. I didnot want to use the dataset already provided instead wanted to go through the process of generating dataset myself to understand how difficult it is to manually label it.

## Stretch Feature 

**Why fourth label** In my dataset of `neutral politics` subreditt (originally contained 1000 posts with approx 9 classes but three of them nearly totalled less than 200) so I decided to add 4th class which will make it 200 and I will also have imabllanced. Using claude on this original Reditt post i recd 125 Executive branch , 35 Foreign policy and 40 Elections(3 label classes). I had a Smaller pool of 19 "Legal and Investigations". Inorder to understand the categories. I  then noticed Claude had mislabled many of the "executive Branch" and "Legal and Investigation class" . I saw legal and Elections also having some misclassifications by Claude. So my thought process was that I should manually lable it and then decide where I should add 50 more so i will have problems in classifying which will help me learn the nuances.

When I relabled it the 2nd time I got - Elections -77 , Executive Branch-68 , Legal and investigations- 68 , Foreign Policy-37. So I took 50 synthetic posts(manually verfiedf and relabled) generatd by gemini an added 50 for Foreign Policy there by creating a set of 300 lables and improving the class imballance as a side effect. 

## Stretch Feature - 50 extra examples for the thinnest label
**My Thought process and execution strategy**

When I relabled it the 2nd time I got - Elections -77 , Executive Branch-68 , Legal and investigations- 68 , Foreign Policy-37. So I took 50 synthetic posts(manually verfiedf and relabled) generatd by gemini an added 50 for Foreign Policy there by creating a set of 300 lables and improving the class imballance as a side effect. 


## Label Taxonomy

The label names below match `./reditt_labels.csv` after trimming surrounding whitespace; the CSV spells the legal category `Legal and investigations`. All examples are quoted directly from the file's `text` column. Record numbers count data records, excluding the header.

I plan to use four categories.

<!-- ⚠️ WHAT CHANGED : labels.csv when I started working on Unit 6 I found misclassification and realized my labeling was not correct. I went through all the labels and changed lables base dont he definitions so as the classification improves and the training dataset is good quality. -->

**What Changed in dataset:  labels.csv when I started working on Unit 6 I found misclassification and realized my labeling was not correct. I went through all the labels and changed lables based ont he definitions so as the classification improves and the training dataset is good quality**

### `Executive Branch`

**Definition:** Posts primarily about presidential or executive agency powers, appointments, administration, policy actions, presidential wrongdoing, presidential statements, presidential actions including justice department and FBI.

**Example 1:** (record 10)

> Is the Special Government Employee carve-out in Executive Order 14158 standard practice for temporary federal organizations ?

**Example 2:** (record 52)

> Is the pace of President Trump's Cabinet appointments similar to those of previous administrations?

### `Legal and investigations`

**Definition:** Posts primarily about courts, investigations, alleged wrongdoing, legal proceedings, or whether a specific action violates the law.

**Example 1:** (record 80)

> Robert Mueller has been appointed a special counsel for the Russia probe. What is that and how does it work?

**Example 2:** (record 85)

> What is the legal interpretation of the SCOTUS' refusal to grant cert to an appeal?

### `Foreign Policy`

**Definition:** Posts primarily about international relations, diplomacy, sanctions, treaties, military conflict, or relations between countries.

**Example 1:** (record 161)

> What are the implications of the US backing out of UNESCO?

**Example 2:** (record 162)

> What incentives and new sanctions does Iran face given US withdrawal from the 'Iran Deal?'

### `Elections`

**Definition:** Posts primarily about voting, campaigns, candidates, polling, election results, or electoral systems and procedures.

**Example 1:** (record 189)

> What are the advantages and disadvantages of unconditionally allowing people to vote by mail?

**Example 2:** (record 185)

> What are the arguments for and against gerrymandering?

### The hardest boundary

**Which two labels:** `Executive Branch` and `Legal and investigations`.

**The decision rule:** Classify the main question being asked. General questions about executive authority, appointments, administration, or policy belong in `Executive Branch`. Questions centered on an investigation, court proceeding, alleged wrongdoing, or a specific violation of law belong in `Legal and investigations`, even when they name the president, the Justice Department, or the FBI. A person's name or an agency's place in government does not decide the label by itself.

**Consistency limit:** This is the intended boundary rule. The current labels do not apply it perfectly:"Did AG Jeff Sessions commit perjury during his confirmation hearing when he didn’t disclose prior meetings with the Russian Ambassador?" labeled as `Legal and investigations`, although its central question concerns alleged wrongdoing but depending on other items being trained on this could be classified as `Executive Branch`. This is a very tricky statement due to the type of post.

In my manual review, Elections and Foreign Policy generally felt clearer to categorize. I expect confusion in both directions between Executive Branch and Legal and Investigations because their subjects overlap.

---

## The Dataset

**Where the posts came from:** Public r/NeutralPolitics posts obtained through a Kaggle Reddit dataset. The selected 200-record dataset used for the examples and counts here is in the location `./labels.csv`

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

<!-- ⚠️ WHAT CHANGED : labels.csv when I started working on Unit 6 I found misclassification and realized my labeling was not correct. I went through all the labels and changed lables base dont he definitions so as the classification improves and the training dataset is good quality. The subreditt gave me 200 records I added another 50 synthetic data collecting through gemini and added at the end so as I have foreign policy as a small dataset and total records is 250 instead of 200.   -->

**Counts per label after relabling but before dding more data to Foreign Policy label class:** 

| Label | Count | Share |
|---|---:|---:|
| Executive Branch | 68 | 27.2% |
| Legal and investigations | 68 | 27.2% |
| Foreign Policy | 37 | 14.8% |
| Elections | 77 | 30.8% |
| **Total** | **250** | **100.0%** |

**Counts per label after relabling and adding more data to Foreign Policy class:** 


| Label | Count | Share |
|---|---:|---:|
| Executive Branch | 68 | 22.7% |
| Legal and investigations | 68 | 22.7% |
| Foreign Policy | 87 | 29.0% |
| Elections | 77 | 25.7% |
| **Total** | **300** | **100.0%** |

Now we see foreign policy class is better ballance previously it was 14.8% and now it is 29% while others are between 22 and 27% so if wwe train properly we should be able to get much better classification results

**Three hard cases**

All three posts have misclassification notes in './labels.csv'. 

**1. Jeff Sessions and the Russian Ambassador — record 89**

> *The post:* Did AG Jeff Sessions commit perjury during his confirmation hearing when he didn’t disclose prior meetings with the Russian Ambassador?
>
> *Could have been:* `Legal and investigations` or `Foreign Policy`.
>
> *I chose, because:* `Executive Branch`. I kept the label for the question about the Attorney General's conduct during his confirmation hearing and legal implications of lying under oath. My note flags the reference to another country: mentioning the Russian Ambassador could cause a classifier to select Foreign Policy.

**2. Legal consequences can a sitting POTUS  — record 82**

> *The post:* What legal consequences can a sitting POTUS be subject to if s/he engages in slander or libel??
>
> *Could have been:* `Executive Branch` or `Legal and investigations`, Since POTUS is mentioned it could get classified to Executive Branch.
>
> *I chose, because:* `Legal and investigations` is aligned with "The core subject is legal liability " as noted in the notes in labels.csv.

**3. Donald Trump Jr.'s emails — record 123**

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

I didnt change it as a part of Unit 5 though I did test few beyind this as default is not giving me good result but I will show the results in Unit 6

**Split sizes:** 210 training / 45 validation / 45 test posts, using a stratified 70% / 15% / 15% split of the 300-record dataset with seed 42, as recorded in `takemeter.ipynb`.

| Label | Train | Validation | Test |
|---|---:|---:|---:|
| Executive Branch | 48 | 10 | 10 |
| Legal and investigations | 48 | 10 | 10 |
| Foreign Policy | 61 | 13 | 13 |
| Elections | 53 | 12 | 12 |
| **Total** | **210** | **45** | **45** |

Each category has **10–13 test examples**, so none has fewer than eight. These groups are still small: one additional error changes recall by about 7.7–10 percentage points, and per-class metrics may vary across random seeds.

**What I changed to get better results (copied from takemeter.ipynb file) as a part of 2nd run**
- in the takemeter.ipynb the training function I have modified to run larger epoch and avoid overfitting . It continues to train till the Validation error goes down till max epoch is exhausted (intend to use upto 25) . While doing this it checkpoints the model so as it can revert back to the best performing model (it stops at the point where the validation error is the lowest and the subsequent next two validation error is higher)    
- I also tried using a lopwer learnign rate ie used 1e-5 but the overall perfromance did not improve but will use the lower parameter as that should give us better perfromance of model when epochs are higher.
- EPOCHS = 25 .. it skipps as soon as we have got the best training results once the validation loss starts increasing
- LEARNING_RATE = 1e-5 .. reduced so as the learning gets better without sacrificing training speed
- BATCH_SIZE = 16

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

I used claude ideas for inital class identification and then manually reviewed and changed labels and deleted rows which didnt match my needed labels

## Stretch Feature 

**Results of Second training Run**

The latest test metrics below come from the Section 5 evaluation in `takemeter.ipynb`, saved in [results.json](results.json), for seed 42 and 45 held-out posts. The separate Section 7 confusion matrix below is retained from the earlier 350-record experiment with 53 test posts.

## Stretch Feature — 2nd training run

<!-- below values changed due to relabling-->

#### Section 5 results from takemeter.ipynb — Latest single-seed run

| Measure | Seed 42 |
|---|---:|
| Overall accuracy | 0.644 |
| Macro F1 | 0.555 |
| F1 — `Executive Branch` | 0.421 |
| F1 — `Legal and investigations` | 0.167 |
| F1 — `Foreign Policy` | 0.774 |
| F1 — `Elections` | 0.857 |

**Accuracy: 64.44%. Macro F1: 0.5548.** The model correctly classified **29 of 45** test posts. Elections has the highest per-class F1 (**0.857**), and Legal and investigations has the lowest (**0.167**). The latest `results.json` contains only seed 42, so cross-seed averages and spread cannot be calculated from this file.
The overall results are ok but two classes ie "Executive Branch" an dthe "Legal and Investigations" needs to better and if we run high er number of epochs I am sure will give better reutls and we will do that in Unit 6


<!-- Changed the stretch feature related information below. as I relabled manually and went through each row the distribution changed The Executive branch came down and "Legal and investigations" went up. The "Foreign policy" now had the lowest and and I had to autgment it by 50 more rows -->

## Stretch Feature 

<!-- old content -->
50 extra examples for the thinnest label In the earlier section when i discussed dataset I already covered how I augemtented the original 200 and in steps of 50 per class and performed various results. During this execrcise I also proved a better balanced dataset (or where class imballance is not extreme) results get better assuming we ahve good number of rows of data per class(mine were between 80 and 98)

<!-- updated content -->

**50 extra examples for the thinnest label- Foreign policy**
In the earlier section when i discussed dataset I already covered how I augemtented the original 200 from reditt and then added additional relabled additional data generated by gemini that gave me 250 rows  but we had sever class imbalance in foreign policy and I added 50 more synthetic data  from gemini  for the Foreign policy label/class thus making it total 300 rows.I still kept the original Executive branch and the "Legal and investigation class"

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
