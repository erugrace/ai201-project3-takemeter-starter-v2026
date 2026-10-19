# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.


### Criterion 1: Overall Accuracy

The model should achieve at least 70% accuracy on the held-out test set.

**Reason:** I chose 70% because some Reddit comments can be complicated or ambiguous. A comment might fit more than one label, making it difficult for the model to classify correctly. I believe 70% is a reasonable target that allows for some mistakes while still showing that the model has learned useful patterns.

### Criterion 2: Per-Label Performance

Each label (`statement`, `reaction`, and `speculation`) should achieve an F1 score of at least 0.60 on the held-out test set.

**Reason:** I chose 0.60 because some comments might be difficult to classify correctly, especially statements and speculations, which can be similar. Sometimes, a comment might express an opinion while also predicting something. I think 0.60 is a reasonable minimum for each label, even when these overlaps make classification difficult.

### Criterion 3: Dataset Balance

No single label should represent more than 70% of the labeled dataset.

**Reason:** I chose 70% because some topics in r/nba might naturally have more statements than reactions or speculations. I want to allow for these differences while making sure that the dataset still contains examples of all three labels.

### Criterion 4: Human Labeling Consistency

Two people independently labeling the same 30 Reddit comments should agree on at least 80% of the labels.

**Reason:** I chose 80% because even though some comments can fit more than one category, I want my definitions to be clear enough for different people to agree on most of the labels. This would show that my categories are consistent and understandable.

### Criterion 5: Prediction Confidence

Among predictions where the model reports 80% confidence or higher, at least 80% should be correct on the held-out test set.

Reason: I chose 80% because I want the model's confidence to reflect how reliable its predictions are. If the model is highly confident about a label, I expect it to be correct most of the time. This would help determine how much its predictions can be trusted.

If there are no predictions with at least 80% confidence, I will report that there were not enough qualifying predictions to evaluate this criterion.
---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

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

## 1.

<!-- Your criterion. It must name a number. -->



**Why this target:**



---

## 2.

<!-- Your criterion. -->



**Why this target:**



---

## 3.

<!-- Your criterion. -->



**Why this target:**



---

## 4.

<!-- Your criterion. -->



**Why this target:**



---

## 5.

<!-- Your criterion. -->



**Why this target:**



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
