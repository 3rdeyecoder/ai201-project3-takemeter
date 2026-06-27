# ai201-project3-takemeter

# TakeMeter

An NLP text classification project developed for AI201. The goal is to classify posts from an online community into discourse categories using a fine-tuned DistilBERT model.

---

## Project Goal

TakeMeter analyzes community discussions and classifies each post into one of several discourse categories. The project compares a zero-shot LLM baseline against a fine-tuned DistilBERT classifier.

---

## Dataset

- 200+ manually labeled posts
- CSV format
- Publicly collected data
- Balanced label distribution

---

## Labels

| Label | Description |
|--------|-------------|
| Analysis | Evidence-based reasoning and structured arguments |
| Hot Take | Strong opinions with little supporting evidence |
| Reaction | Emotional responses to events |

(Replace these with your actual labels.)

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

Results are available in:

- evaluation_results.json
- confusion_matrix.png

---

## Repository Structure

README.md
planning.md
labeled_dataset.csv
evaluation_results.json
confusion_matrix.png

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
