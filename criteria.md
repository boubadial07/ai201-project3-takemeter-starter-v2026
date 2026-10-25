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


## 1. Overall accuracy

The trained classifier will achieve at least **0.60 overall accuracy** on the held-out test set across all three evaluation seeds.

**Why this target:** There are three labels, so a roughly balanced random guesser would achieve about 0.33 accuracy. A target of 0.60 requires a meaningful improvement over that baseline without assuming a small dataset will produce near-perfect results.

---

## 2. Per-label performance

Each label will achieve an **F1 score of at least 0.50** on the held-out set across all three evaluation seeds.

**Why this target:** `analysis`, `hot_take`, and `reaction` can overlap when a comment contains both an opinion and an emotion. Requiring a minimum F1 score for every label helps prevent strong performance on `analysis` from hiding poor recognition of the other two labels. This target may need revisiting if a label has very few test examples, but it must not be lowered merely because the model misses it.

---

## 3. Label balance

The largest label in the final labeled dataset will contain **no more than twice as many examples as the smallest label**.

**Why this target:** `analysis` comments are relatively easy to find because gaming discussions often include explanations and comparisons. Short `reaction` comments may be less common in the examples collected so far. Limiting the imbalance reduces the risk that the model mostly learns the most common label.

---

## 4. Annotation consistency

A second reviewer will agree with my assigned label on at least **80% of a sample of 20 posts**.

**Why this target:** The hardest boundary in this taxonomy is between `hot_take` and `reaction`. Checking agreement on 20 posts can reveal whether the decision rule is clear enough for another person to apply consistently. If a second reviewer is unavailable, this criterion cannot be measured as written.

---

## 5. Confidence calibration

Among predictions with confidence scores of **at least 0.80**, at least **80% will be correct** on the held-out set, provided there are at least 10 such predictions.

**Why this target:** A confident prediction should be more trustworthy than an uncertain one. Because the taxonomy includes ambiguous comments and the dataset is expected to be small, I want to check whether high-confidence predictions are actually reliable rather than treating confidence as proof of correctness.


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
