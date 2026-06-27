# TakeMeter

An NLP text classification project developed for AI201. The goal is to classify posts from an online community into discourse categories using a fine-tuned DistilBERT model.

---

## Project Goal

TakeMeter analyzes community discussions and classifies each post into one of several discourse categories. The project compares a zero-shot LLM baseline against a fine-tuned DistilBERT classifier.

---

## Dataset

- `labeled_dataset.csv` — 200 manually labeled public posts from r/nba (columns: `text`, `label`, `notes`).
- CSV format, single file (not pre-split). The notebook performs the 70%/15%/15% split.


---

## Labels

| Label | Description |
|--------|-------------|
| analysis | Evidence-based reasoning and structured arguments (stats, comparisons, tactical claims) |
| hot_take | Bold subjective claims stated without supporting evidence |
| reaction | Short, emotional responses to events |

---

## Model

- distilbert-base-uncased
- HuggingFace Transformers
- Google Colab
- T4 GPU

---

## Evaluation

Metrics used:

- Accuracy
- Precision
- Recall
- F1 Score
- Confusion Matrix


Results from the current Colab run:

- Baseline accuracy: 0.9333
- Fine-tuned accuracy: 0.7333
- Improvement: -0.20 (fine-tuned model underperformed vs baseline)
- Test set size: 30

Results are saved in:

- `evaluation_results.json`
- `confusion_matrix.png`


Results (to be produced by the Colab notebook) are expected in:

- `evaluation_results.json` (baseline vs fine-tuned metrics and per-class numbers)
- `confusion_matrix.png` (image produced by the notebook)

---

## Repository Structure

- README.md
- planning.md
- labeled_dataset.csv
- evaluation_results.json (placeholder — replace with Colab export)
- confusion_matrix.png (placeholder — replace with Colab export)

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
