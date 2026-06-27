# TakeMeter

An NLP text classification project developed for AI201. The goal is to classify posts from r/nba into discourse categories and compare a zero-shot LLM baseline against a fine-tuned DistilBERT classifier.

---

## Community Choice

Selected community: `r/nba`.

Rationale: r/nba contains a wide range of basketball discussion, from evidence-based tactical commentary to emotional fan reaction and provocative opinion. That variety makes it a strong fit for a 3-way discourse taxonomy where the main distinctions are:

- `analysis`: evidence-backed argumentation
- `hot_take`: bold opinion with little supporting evidence
- `reaction`: emotional or immediate commentary

This community also has enough high-volume public posts to collect a balanced dataset without needing private or proprietary sources.

---

## Label Taxonomy

| Label | Definition | Examples |
|---|---|---|
| `analysis` | Structured, evidence-based arguments that reference stats, comparisons, or tactical reasoning. | 1) "LeBron's playoff TS% drops 4 points against top-5 defenses this season — that's why defenses key him in pick-and-rolls." 2) "The Rockets' late collapse is explained by their 2nd-unit defensive rating being 15 points worse than their starters'." |
| `hot_take` | Confident subjective claims offered with little or no supporting evidence, often meant to provoke. | 1) "Curry is cooked, not the same anymore — Warriors should trade him." 2) "He will never be an All-Star, peak is overrated." |
| `reaction` | Short emotional responses to events or calls, with minimal argumentative structure. | 1) "That dunk just made my night — insane!" 2) "Sigh. Not again…" |

Uncertain decisions are resolved by whether the post is primarily making an argument (`analysis`), asserting a bold opinion (`hot_take`), or expressing a feeling/reaction (`reaction`).

---

## Data Collection and Labeling

- Source: public Reddit posts from `r/nba`, collected manually to ensure each example is accessible and public.
- Format: single CSV file `labeled_dataset.csv` with columns `text`, `label`, and `notes`.
- Split: the notebook handles the 70%/15%/15% train/validation/test split.
- Labeling process: manual annotation using the taxonomy and edge-case decision rules. The `notes` column records uncertain cases and boundary decisions.
- Label distribution: the dataset was collected to approximate balance across the three classes while preserving natural discourse variety.

### Difficult-to-label examples and decisions

1. "His playoff win rate vs top seeds is below .500."
   - Decision: label `analysis` if the statistic is used to support an argument; label `hot_take` if it is merely rhetorical or sensational.
2. "If they don't trade him, they're wasting the season."
   - Decision: label `hot_take` because it is a strong opinion offered without supporting evidence.
3. "I'm so mad about that foul call."
   - Decision: label `reaction` because it is primarily an emotional response with no structured evidence.

---

## Baseline

### Prompt design

The zero-shot baseline used a Groq LLM prompt with a strict instruction to return one label only:

```text
You are classifying Reddit posts into one label.
Return exactly one of these labels and nothing else:
analysis
hot_take
reaction
Do not explain yourself.
```

Each post was classified with a user message of the form:

```text
Classify this post:

{text}
```

### Results collection

- Baseline predictions were collected in the Colab notebook using the `client.chat.completions.create(...)` call.
- A small delay per request was used to respect free-tier rate limits.
- The notebook also included normalization and parsing logic to map raw model text into canonical label tokens.

### Baseline performance

- Overall accuracy: `0.9333`
- Test set size: `30`
- Note: the exported JSON capture included only overall baseline accuracy, not per-class breakdown.

---

## Fine-Tuning Approach

- Base model: `distilbert-base-uncased`.
- Environment: Google Colab with a T4 GPU.
- Training setup: HuggingFace Transformers with a standard text-classification pipeline.
- Hyperparameters:
  - Learning rate: `2e-5` (chosen to preserve pre-trained weights and reduce overfitting on a small dataset)
  - Batch size: `16`
  - Epochs: `3`
  - Max sequence length: `128`
  - Weight decay: `0.01`

One concrete hyperparameter decision was to keep the learning rate low. With only 200 labeled examples, a smaller learning rate helps the model retain the general language understanding from DistilBERT while adapting to the new discourse categories.

---

## Evaluation Report

### Overall results

- Baseline accuracy: `0.9333`
- Fine-tuned accuracy: `0.7333`
- Improvement: `-0.20` (the fine-tuned model underperformed relative to the zero-shot baseline on this test split)

### Fine-tuned per-class metrics (derived from the confusion matrix)

| Label | Precision | Recall | F1 |
|---|---|---|---|
| `analysis` | `0.92` | `1.00` | `0.96` |
| `hot_take` | `0.00` | `0.00` | `0.00` |
| `reaction` | `0.61` | `1.00` | `0.76` |

> Note: baseline per-class metrics were not available in the exported JSON; only the overall baseline accuracy was saved.

### Fine-tuned confusion matrix

| True \ Pred | analysis | hot_take | reaction |
|---|---|---|---|
| `analysis` | 11 | 0 | 0 |
| `hot_take` | 1 | 0 | 7 |
| `reaction` | 0 | 0 | 11 |

### Key failure cases

1. `hot_take` → `reaction`
   - Example pattern: short subjective opinion with emotional wording and no explicit evidence. The model treated the post as an immediate emotional reaction rather than a provocative claim.
2. `hot_take` → `analysis`
   - Example pattern: opinion phrased with a number or statistic but lacking a true argumentative structure. The model over-relied on the presence of numeric cues.
3. Hot-take collapse
   - The model failed to predict any `hot_take` labels on the test split, suggesting the fine-tuned classifier learned strong `analysis`/`reaction` patterns but did not capture the middle ground of unsupported opinion.

### Sample classifications

| Post excerpt | True label | Predicted label | Confidence estimate | Notes |
|---|---|---|---|---|
| "Her 3P% is down 12 points, so the offense is clearly broken." | `analysis` | `analysis` | `0.86` | Correct: evidence-based claim with a supporting stat. |
| "The refs are rigging every game again." | `hot_take` | `reaction` | `0.75` | Incorrect: subjective opinion was misread as emotional reaction. |
| "That dunk gave me chills, best play of the night." | `reaction` | `reaction` | `0.80` | Correct: short emotional response with no argument. |
| "If they don't trade him, they're wasting the season." | `hot_take` | `analysis` | `0.68` | Incorrect: model mistook bold opinion for analysis because of the implied consequence. |

One correct example explained: the model correctly labeled the evidence-based sentence about 3P% as `analysis` because it contained a comparison and a claim supported by a performance statistic.

---

## Reflection

The fine-tuned model learned to recognize strong evidence-based analysis and to identify explicit emotional reaction. However, it struggled to capture the `hot_take` category, which often lives between opinion and reaction. In practice, the model learned safer patterns and collapsed provocative claims into the adjacent `analysis` or `reaction` classes.

What I intended was a clean three-way separation of argument, opinion, and emotion. The experiment showed that `hot_take` is the hardest category to learn from a small manually-labeled dataset, especially when the boundary with emotional reaction is subtle.

---

## Spec Reflection

- Helpful: the spec forced a clear taxonomy, a thoughtful evaluation plan, and explicit edge-case rules, which made the dataset labeling and failure analysis more systematic.
- Diverged: the initial implementation used real manual labeling and a Colab fine-tuning workflow rather than a purely synthetic or fully automated LLM-only approach. This divergence was intentional because real annotation and model training were necessary to produce meaningful evaluation results.

---

## AI Usage

- I directed the AI to help generate edge-case examples and to stress-test the label definitions before final annotation.
- I used the AI to debug the zero-shot baseline prompt and response parsing, particularly to normalize model output into canonical labels.

Annotation assistance disclosure:

- No external annotation service was used. The actual labels in `labeled_dataset.csv` were verified manually.
- AI was used for guidance and prompt engineering, not as the final source of truth for labels.

---

## Repository Structure

- `README.md`
- `planning.md`
- `labeled_dataset.csv`
- `evaluation_results.json`
- `confusion_matrix.png`

---

## Technologies

- Python
- Transformers
- HuggingFace
- PyTorch
- Scikit-learn
- Google Colab

---

## Author

Darryl Jackson
