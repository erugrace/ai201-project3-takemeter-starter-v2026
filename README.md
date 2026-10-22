# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv
> ```
>
> Then open `takemeter.ipynb` in this folder using VS Code or Jupyter Notebook. Select the `.venv` kernel inside the project and run the notebook sections in order.
>
> Everything runs locally on my machine. No API keys or external accounts are required for training.

---

## What This Does

TakeMeter is a text classification project that uses machine learning to identify different types of comments from Reddit's r/nba community.

The model classifies comments into three categories: `statement`, `reaction`, and `speculation`. I collected and labeled 200 Reddit comments and used them to fine-tune a DistilBERT model.

The goal is to see how well the model can recognize the main purpose of a comment, even when different categories overlap.

---

## Milestone 1: Community Exploration

### Selected Community

**r/nba — Reddit's NBA Community**

I chose r/nba because it is an active community where people discuss basketball games, players, team performances, and other NBA-related topics.

For this project, I focused on Reddit comments rather than the main posts because comments contain different forms of communication, including opinions, reactions, predictions, and suggestions.

### Observations

After reviewing 27 comments from different basketball discussions, I noticed several patterns:

1. **Opinions and judgments:** Many commenters express personal opinions about players, teams, and rankings. Some provide reasons for their judgments, while others simply state what they believe.

2. **Emotional reactions:** Some comments mainly express excitement, frustration, disappointment, surprise, or humor rather than making an argument.

3. **Predictions and speculation:** Some commenters discuss what they believe might happen in future games, seasons, or players' careers.

4. **Suggestions and questions:** Some comments propose ideas or actions, while others ask questions or raise hypothetical situations.

### Initial Findings

One challenge I noticed was that some comments could fit more than one category. For example, a prediction might also express an opinion, and a suggestion might include an emotional reaction.

Another observation was that questions do not always seek information. Some commenters use questions to criticize or challenge another person's argument.

These observations helped me design clearer classification labels and decision rules in Milestone 2.

---

## Label Taxonomy

### statement

**Definition:**

A comment whose main purpose is to make a claim, express a judgment or recommendation, or describe a current or past situation.

**Example 1:**

> Nuggets way too low

**Example 2:**

> He's focused and has been practicing his shot all summer.

### reaction

**Definition:**

A comment whose main purpose is to express an immediate feeling, such as excitement, frustration, surprise, or amusement.

**Example 1:**

> Fantastic

**Example 2:**

> Fuck I just wanna see a healthy Steph for another season man

### speculation

**Definition:**

A comment whose main purpose is to predict an uncertain future event or explore a hypothetical situation.

**Example 1:**

> LaMelo Ball will guide the Wolves to the promised land

**Example 2:**

> Got me thinking, if Bronny had a kid this year, would LeBron be the first active NBA grandpa?

### The Hardest Boundary

**Which two labels:**

Statement vs. Speculation

**The decision rule I used every time:**

When a comment expresses an opinion about what someone should do, I will classify it as `statement`. However, if the comment predicts what might happen in the future or discusses a hypothetical situation, I will classify it as `speculation`, even if it contains a personal opinion.

### Taxonomy Stress Test

I tested my three labels against eight borderline comments to see how consistently they could be applied.

One of the main challenges was distinguishing opinions about future actions from actual predictions.

To address this, I decided that recommendations about what someone should do belong under `statement`, while predictions and hypothetical situations belong under `speculation`.

---

## The Dataset

**Where the posts came from:**

I collected 200 public comments from different discussions in the r/nba subreddit. These discussions included player performances, team rankings, game results, and predictions about future games.

**How I labelled them:**

I started by labeling the first 20 comments and recording them as `cold` examples. For the remaining 180 comments, I used ChatGPT to suggest labels and identify borderline cases.

I then reviewed the suggested labels and made corrections where necessary. I classified each comment according to its main purpose rather than focusing only on individual words.

**Counts per label:**

| Label | Count | Share |
|---|---:|---:|
| statement | 108 | 54% |
| reaction | 60 | 30% |
| speculation | 32 | 16% |
| **Total** | **200** | **100%** |

No label exceeds the required 70% limit.

### Three Hard Cases

**1.**

**The post:**

> We’re winning 4 games this season! WOOOOO

**Could have been:** Reaction or Speculation

**I chose:** Speculation, because the comment predicts how many games the team will win in the future, even though the person expresses excitement.

**2.**

**The post:**

> There should be a mercy rule at this point

**Could have been:** Statement or Speculation

**I chose:** Speculation in my reviewed dataset. However, based on my decision rule, this should be reconsidered as `statement` because it recommends a rule change rather than predicting an outcome.

**3.**

**The post:**

> I think Kobe would've been proud of him

**Could have been:** Statement or Speculation

**I chose:** Speculation because the comment imagines how Kobe might have felt in a hypothetical situation.

---

## The Training Run

**Base model:**

`distilbert-base-uncased` — DistilBERT for sequence classification.

**Settings:**

| Setting | Value |
|---|---|
| Epochs | 3 |
| Learning rate | `2e-5` |
| Batch size | 16 |
| Maximum tokens per comment | 128 |
| Seed | [Enter seed from Section 2] |
| Training device | MPS (Apple Silicon GPU) |

**Anything I changed from the defaults, and why:**

I kept the default training settings for my first run. I configured the label map to match the three categories in my dataset: `statement`, `reaction`, and `speculation`.

**Split sizes:**

| Label | Train | Validation | Test |
|---|---:|---:|---:|
| statement | 75 | 17 | 16 |
| reaction | 42 | 9 | 9 |
| speculation | 22 | 5 | 5 |
| **Total** | **139** | **31** | **30** |

The test set contained only five `speculation` examples. This means that its evaluation metrics may change significantly between training runs because even one incorrect prediction can make a noticeable difference.

### Initial Training Results

The model completed three epochs and achieved the following results on the held-out test set:

| Metric | Result |
|---|---:|
| Overall accuracy | 53

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline |
|---|---|
| Overall accuracy | 0.567 |
| Macro F1 | 0.399 |
| F1 — `reaction` | 0.000 |
| F1 — `speculation` | 0.500 |
| F1 — `statement` | 0.698 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

I predict my trained model will beat the baseline most on statement label, because a lot more data appeared as statements.

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.567 | 0.533 | -0.033 |
| Macro F1 | 0.399 | 0.232 | -0.167 |
| F1 — `reaction` | 0.000 | 0.000 | +0.000 |
| F1 — `speculation` | 0.500 | 0.000 | -0.500 |
| F1 — `statement` | 0.698 | 0.696 | -0.002 |

My prediction didn't hold up. 


**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



### What my definitions were worth

I ran the baseline twice on the same 30 held-out posts: once with my label definitions (`label_definitions.txt`) and once with only the bare label names. The baseline reported above is the run **with definitions** (`baseline_results.json`). The bare-names run is saved in `baseline_bare_names.json`.

| Measure | Bare label names | With my definitions | Difference |
|---|---|---|---|
| Overall accuracy | 0.567 | 0.567 | 0.000 |
| Macro F1 | 0.403 | 0.399 | −0.004 |
| F1 — `reaction` | 0.522 | 0.000 | −0.522 |
| F1 — `speculation` | 0.000 | 0.500 | +0.500 |
| F1 — `statement` | 0.688 | 0.698 | +0.010 |

Overall, my definitions were worth **0 points of accuracy**. Both runs got 17 of 30 posts right. However, the definitions changed *which* posts were right:

- **With bare names**, the model found 6 of the 9 `reaction` posts but none of the 5 `speculation` posts.
- **With my definitions**, it found 2 of the 5 `speculation` posts but none of the `reaction` posts. It labelled almost everything `statement` (15 of 16 real statements were caught, but many reactions were pulled in too).

My `speculation` definition ("predict an uncertain future event or explore a hypothetical situation") seems to give the model a clearer cue than the single word "speculation". My `statement` definition is broad ("make a claim, express a judgment..."), and emotional comments like "What a series, wow" also express a judgment. So with definitions, `statement` swallows `reaction`. This is the same overlap I noticed in Milestone 1, and it suggests my `reaction` and `statement` definitions need a clearer boundary.

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
