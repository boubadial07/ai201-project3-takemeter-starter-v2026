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

TakeMeter is a text classification project focused on gaming community discussions. It uses a fine-tuned DistilBERT model to classify gaming-related comments into three categories: analysis, hot_take, and reaction. The classifier distinguishes between comments that provide reasoning, strong opinions without supporting explanations, and immediate emotional responses. The goal is to explore how effectively a machine learning model can identify the primary purpose of gaming-related comments.
---

## Label Taxonomy

### 1. `analysis`
A post that primarily evaluates or explains a game using specific reasons, examples, observations, or comparisons.

- **Example 1 — Everspace (`Angzt`):** "The dogfights are pretty arcadey with quick maneuvers, a bunch of weapon types, and special abilities. The first is a roguelite, but the second is not. Both (but especially the second) have a bunch of well-hidden loot in their areas (often behind small puzzles) which can take a while to find."
- **Example 2 — Racing game (`Moopies`):** "I relieved myself of a lot of stress once I accepted that there are more builds than "Go fastest, make best turns" that will win a race. It's overall more fun, and the variety is something that keeps me coming back."

### 2. `hot_take`
A post that primarily makes a strong evaluative judgment without providing meaningful supporting reasons or examples.

- **Example 1 — NieR: Automata (`SunnyWynter`):** "Definitely agree with you on Nier Automata. The game is just really bland in terms of gameplay and story."
- **Example 2 — Superman 64 (`Make_It_Plain`):** "Been playing Superman 64 for about a week now. Suffice it to say, I think it's one of the greatest games ever made."

These are candidate examples because the comments make strong judgments without explaining them in detail. The Superman 64 comment may be sarcastic, but the screenshot alone does not establish that.

### 3. `reaction`
A post that primarily expresses an immediate emotion or personal response rather than developing a reasoned evaluation.

- **Example 1 — amazinglover (sentence excerpt):** "I absolutely hate this game."
- **Example 2 — frostysnow:** "looks awesome, can't wait to try it"

The first example is the opening sentence of a longer comment that later expresses conflicting feelings. The second primarily communicates excitement and anticipation.

### Decision Rule: Choosing the Primary Purpose

Classify each post by its primary purpose, rather than by every feature it contains.

- Use `analysis` when the author explains an evaluation with specific reasons, examples, observations, or comparisons.
- Use `hot_take` when the author makes a strong evaluative judgment without meaningful supporting reasoning in the comment.
- Use `reaction` when the main purpose is to express an immediate emotion or personal response.

The hardest boundary is between `hot_take` and `reaction`. A strong statement about whether a game is good or bad is usually `hot_take` when it makes an unsupported evaluation. A statement primarily expressing excitement, frustration, sadness, or joy is `reaction`. When a comment combines several purposes, choose the label that best represents its main purpose.

**Which two labels are easiest to confuse?**

`hot_take` and `reaction` are the easiest to confuse because both can use strong or emotional language. I distinguish them by purpose: `hot_take` expresses an evaluative opinion or judgment, while `reaction` primarily expresses an immediate personal emotion or response.

**The decision rule I used every time:**

I asked whether the post primarily explains or supports an evaluation, expresses an opinion without meaningful supporting reasoning, or communicates an immediate emotional response. I assigned the label that best represented the post's primary purpose rather than relying only on its tone.



---

## The Dataset

**Where the posts came from:**

The dataset contains 219 gaming-related examples. I manually collected 201 comments from Reddit and added 18 synthetic examples with AI assistance to improve the representation of the categories. I reviewed the examples and applied my classification rules before including them in the final dataset.

**How I labeled them:**

I reviewed the comments and assigned each one to one of three categories: `analysis`, `hot_take`, or `reaction`, based on its primary purpose. I also used ChatGPT as an AI-assisted resource during the dataset development process, including sharing comments and discussing classification decisions. The final labels were reviewed and organized into the dataset.

**Counts per label:**

| Label | Count | Share |
|---|---:|---:|
| analysis | 99 | 45.2% |
| hot_take | 70 | 32.0% |
| reaction | 50 | 22.8% |
| **Total** | **219** | **100%** |

**Data split:**

The 219 labeled examples were divided into three sets:

| Dataset | Number of posts |
|---|---:|
| Training | 153 |
| Validation | 33 |
| Testing | 33 |
| **Total** | **219** |

**Label distribution across splits:**

| Label | Training | Validation | Testing |
|---|---:|---:|---:|
| analysis | 69 | 15 | 15 |
| hot_take | 35 | 8 | 7 |
| reaction | 49 | 10 | 11 |

**Note:** The `hot_take` category has only 7 examples in the test set. Metrics for this category may vary considerably between random seeds because of the small sample size.

**Three hard cases**

1. Nier: Automata
October 25, 2026 · Username: SunnyWynter
“Definitely agree with you on Nier Automata. The game is just really bland in terms of gameplay and story.”

- Candidate label: hot_take
- Could have been: analysis
- Why: It makes a strong negative evaluation, but doesn't provide supporting examples or detailed reasoning.
2. Superman 64
October 25, 2026 · Username: Make_It_Plain
“Been playing Superman 64 for about a week now. Suffice it to say, I think it’s one of the greatest games ever made.”

- Candidate label: hot_take
- Could have been: reaction
- Why: It expresses a strong evaluation. However, the wording may be sarcastic, so the intended meaning is uncertain without more context.
3. Gaming excitement
October 25, 2026 · Username: frostysnow
“looks awesome, can't wait to try it”

- Candidate label: reaction
- Could have been: hot_take
- Why: It primarily communicates excitement and anticipation rather than an argument or evaluation supported by reasoning.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Model type:** Fine-tuned DistilBERT sequence classifier with three output labels: `analysis`, `hot_take`, and `reaction`.

**Training configuration:**
- Epochs: 3
- Learning rate: `2e-5`
- Batch size: 16
- Maximum sequence length: 128 tokens

**Anything I changed from the defaults, and why:**

For the initial training run, I used the starter project's default training configuration. During Unit 6, I introduced class-weighted loss as an improvement to address the model's difficulty recognizing the underrepresented `hot_take` category. The class weights gave greater importance to categories with fewer training examples during optimization.

**Split sizes:**

- Training: 153 posts
- Validation: 33 posts
- Testing: 33 posts
- Total: 219 posts
The test set contains only 7 `hot_take` examples, which can contribute to metric variability across random seeds.

---

## How I Used AI

I used ChatGPT as an AI-assisted resource during dataset development. I shared Reddit comments in the conversation, discussed classification decisions, and received suggestions for additional `hot_take` examples. I reviewed the suggested comments and manually assigned their final labels using my classification rules.

AI assistance supported the data collection and labeling process, but I remained responsible for reviewing the examples, applying the classification rules, and preparing the final dataset.

**Moment 1**

- **What I asked for:** Help distinguishing between `hot_take` and `reaction` when classifying gaming comments.
- **What came back:** ChatGPT explained how to distinguish strong evaluative opinions from immediate emotional responses.
- **What I changed:** I applied the distinction to the comments and made the final labeling decisions based on each comment's primary purpose.

**Moment 2**

- **What I asked for:** Additional gaming-related examples that could represent the `hot_take` category.
- **What came back:** ChatGPT suggested additional comments and examples.
- **What I changed:** I reviewed the suggestions, evaluated whether they fit my taxonomy, and manually assigned the final labels.

**Pre-labelling disclosure:**

I used ChatGPT during the initial dataset development and labeling process. I shared Reddit comments and received AI assistance with suggesting and discussing category labels. I reviewed the suggestions, applied my classification rules, and made the final labeling decisions. Some additional `hot_take` examples were also suggested by ChatGPT and manually reviewed before being included in the dataset.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

The baseline model achieved an overall accuracy of 0.364 and a macro F1 score of 0.308. My fine-tuned DistilBERT model achieved an accuracy of 0.455, an improvement of 0.091. However, its macro F1 decreased by 0.100.

| Measure | Baseline | Trained | Difference |
|---|---:|---:|---:|
| Overall accuracy | 0.364 | 0.455 | +0.091 |
| Macro F1 | 0.308 | 0.208 | -0.100 |
| F1 — `analysis` | 0.118 | 0.625 | +0.507 |
| F1 — `hot_take` | 0.308 | 0.000 | -0.308 |
| F1 — `reaction` | 0.500 | 0.000 | -0.500 |

The fine-tuned model performed better on `analysis`, but struggled to identify `hot_take` and `reaction`. This suggests that fine-tuning improved recognition of one category without learning reliable boundaries across all three labels.

**My Expected Outcome (Retrospective)**

Before reviewing the results, I expected the fine-tuned DistilBERT model to outperform the baseline because it was trained specifically on my gaming-related dataset and custom classification categories. I also expected `analysis` to be easier to recognize than `hot_take` and `reaction`, since analysis comments often contain more descriptive language and supporting details.

**What the gap actually means:**

Fine-tuning improved overall accuracy from 0.364 to 0.455, but macro F1 decreased from 0.308 to 0.208. The model improved substantially at identifying `analysis`, but failed to correctly identify `hot_take` and `reaction` in this initial run. This shows that improving overall accuracy does not necessarily mean the model performs well across every category. The results also suggest that the model struggled with distinguishing between the three labels, especially categories with fewer examples.

---

## Run Log — Before

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.60 | 0.455 | 0.515 | 0.545 | FAIL |
| 2. Every label F1 | ≥ 0.50 | Not all met | Not all met | Not all met | FAIL |
| 3. Largest label ≤ 2× smallest | ≤ 2:1 | 1.98:1 | 1.98:1 | 1.98:1 | PASS |
| 4. Second reviewer agreement | ≥ 80% | Not measured | Not measured | Not measured | NOT MEASURED |
| 5. High-confidence accuracy | ≥ 80% | Not measured | Not measured | Not measured | NOT MEASURED |

### Summary

The dataset met the original label-balance requirement with a ratio of 1.98:1. However, the initial model still struggled to classify the labels consistently, particularly hot_take.

**Confusion Matrix — Before Improvement (Seed 42)**

| Actual \ Predicted | analysis | hot_take | reaction |
|---|---:|---:|---:|
| analysis | 15 | 0 | 0 |
| hot_take | 7 | 0 | 0 |
| reaction | 11 | 0 | 0 |

The original model predicted every test example as `analysis`.

It correctly classified 15 of 15 analysis posts but failed to identify any hot_take or reaction posts.

This shows that the original model strongly favored the analysis category and struggled to distinguish between the three labels.

---

## Verdicts and Diagnoses

| # | Criterion | Target | Before | After | Verdict |
|---|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.60 across all seeds | 0.455–0.545 | 0.576–0.788 | FAIL |
| 2 | Per-label performance | Every label F1 ≥ 0.50 | Not met | `hot_take` failed in Seeds 42 and 7 | FAIL |
| 3 | Label balance | Largest class ≤ 2× smallest | 1.98:1 | 1.98:1 | PASS |
| 4 | Annotation consistency | ≥ 80% agreement on 20 posts | Not measured | 100% on 30 staff-labeled posts | PASS |
| 5 | Confidence calibration | ≥ 80% correct at confidence ≥ 0.80, with at least 10 predictions | Not measured | Not measured at the required threshold | UNMEASURED |

### Diagnoses

1. **Overall accuracy:** Class weighting improved accuracy across all three seeds. However, Seed 42 reached only 0.576, which remained below the original 0.60 target.

2. **Per-label performance:** Analysis and reaction consistently exceeded the 0.50 F1 target after improvement. Hot_take remained below the target in Seeds 42 and 7, with F1 scores of 0.250 and 0.444. This suggests that hot_take remains the most difficult category for the model to recognize consistently.

3. **Label balance:** The dataset contains 99 analysis, 70 hot_take, and 50 reaction examples. The largest class is 1.98 times the smallest, meeting the original balance requirement.

4. **Annotation consistency:** I achieved 100% agreement with the staff reference labels across all 30 posts. This exceeds the numerical 80% target, although the comparison used staff-provided reference labels rather than a separately documented independent reviewer. It provides evidence that I applied the written taxonomy consistently across this sample.

5. **Confidence calibration:** The model produced zero predictions with confidence scores of at least 0.80 across all three evaluation seeds. Because the original criterion requires at least 10 high-confidence predictions, the accuracy at this threshold could not be calculated. This suggests that the model's predictions were generally not confident enough to satisfy the required threshold. The criterion remains unmeasured rather than passed.

### Overall assessment

Class-weighted training substantially improved the model's performance. Average accuracy increased from approximately 50.5% to 69.7%, and average macro F1 increased from approximately 29.6% to 64.0%. However, the model still struggles to classify hot_take consistently, and its confidence calibration needs further evaluation.

---

## Agreement Report

**Agreement rate:** 30 / 30 = 100%

I compared my labels against the staff labels using `agreement.py`. All 30 posts matched the staff labels, with no disagreements.

**Disagreements**

There were no disagreements to adjudicate.

**Interpretation**

I matched the staff taxonomy on all 30 posts. This suggests that I applied the written definitions consistently across this sample. The examples were relatively straightforward to classify, although this result does not guarantee that every borderline case in my original dataset was labeled consistently.

**Disagreement analysis**

There were no disagreements in the 30-post comparison, so there were no cases to adjudicate or categorize into the three piles. As a result, this sample provides no direct evidence of labeling inconsistencies, gaps in the taxonomy, or ambiguous decisions. I will still consider potential labeling inconsistencies when diagnosing the model's performance on my larger dataset.

---

## The Improvement

**What I changed:**

I changed the training process to use class-weighted loss. I calculated weights based on the number of examples in each class and applied them through `WeightedTrainer` and `CrossEntropyLoss`. This gave greater importance to classes with fewer training examples, rather than allowing the model to favor the more common class.

**Which diagnosis pointed at it:**

The earlier evaluation showed that the model struggled to recognize `hot_take` and frequently predicted other labels instead. The confusion matrix also showed that the original model predicted every test example as `analysis` for Seed 42. These results suggested that the model was favoring the more common class and needed to pay more attention to underrepresented labels.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.60 | 0.576 | 0.727 | 0.788 | **Miss** |
| 2. Every label F1 (lowest label) | ≥ 0.50 | 0.250 | 0.444 | 0.667 | **Miss** |
| 3. Label balance | Largest ≤ 2× smallest | — | — | — | **Met (1.98:1)** |
| 4. Annotation agreement | ≥ 80% | — | — | — | **Met (30/30 = 100%)** |
| 5. Confidence calibration | ≥ 80% accuracy at confidence ≥ 0.80, with at least 10 predictions | 0 predictions | 0 predictions | 0 predictions | **Not measurable** |

### Confusion Matrix — After Improvement (Seed 42)

| Actual \ Predicted | analysis | hot_take | reaction |
|---|---:|---:|---:|
| analysis | 9 | 0 | 6 |
| hot_take | 3 | 1 | 3 |
| reaction | 2 | 0 | 9 |

The weighted model correctly classified 9 of 15 analysis posts, 1 of 7 hot_take posts, and 9 of 11 reaction posts.

The largest confusion was 6 actual analysis posts classified as reaction, compared with 2 actual reaction posts classified as analysis. This indicates that the model still has difficulty distinguishing some analysis posts from reaction posts.

Unlike the original model, which predicted every test example as analysis for Seed 42, the weighted model produced predictions across all three categories. However, hot_take remains particularly difficult to classify correctly.

**Did it help, and how do I know:**

Yes, class weighting improved the model's performance compared with the original unweighted model. The original three-seed results had accuracy scores of 0.455, 0.515, and 0.545, while the weighted model achieved 0.576, 0.727, and 0.788.

The weighted model also began producing predictions across all three categories instead of predicting every test example as `analysis` in the original Seed 42 evaluation. However, the improvement was not enough to meet every acceptance criterion. The `hot_take` F1 score remained below 0.50 for two seeds, and the confidence criterion could not be measured because no predictions reached 0.80 confidence.

Overall, class weighting was a useful improvement, but the results show that the model still needs better examples and clearer distinctions between the three categories.

---

## What's Still Broken

Although class weighting improved the model's performance, some problems remain.

1. **Inconsistent overall accuracy:** Seed 42 achieved only 0.576 accuracy, below my 0.60 target. Seeds 7 and 2024 performed better, with accuracy scores of 0.727 and 0.788. This variation shows that the model's performance depends on the evaluation seed.

2. **Difficulty identifying `hot_take`:** The `hot_take` F1 score was 0.250 for Seed 42 and 0.444 for Seed 7, both below my 0.50 target. Seed 2024 reached 0.667. The model still struggles to distinguish unsupported confident opinions from analysis and reactions.

3. **Low prediction confidence:** None of the three seeds produced predictions with confidence of at least 0.80. Therefore, I could not verify whether high-confidence predictions meet my 80% accuracy target.

4. **Remaining confusion between labels:** The Seed 42 confusion matrix shows that six actual `analysis` posts were classified as `reaction`. This suggests that the distinction between evidence-based arguments and personal reactions still needs improvement.

The weighted-loss improvement helped the classifier recognize more than one category and improved overall performance. However, the results are not consistent across all seeds, and `hot_take` remains the most difficult category. More carefully reviewed training examples, especially borderline `hot_take` examples, may help improve the model.

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
