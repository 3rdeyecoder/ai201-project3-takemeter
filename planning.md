# Project Planning

## 1) Community

Selected community: r/nba (public subreddit for NBA discussion).

Rationale: r/nba hosts a high volume of text discussion that ranges from play-by-play reaction to deep statistical analysis and tactical argument. This variety makes it a good fit for a 3-way discourse classification (`analysis`, `hot_take`, `reaction`) because distinctions (evidence-backed analysis vs. confident unsupported claims vs. emotional reaction) map to real conversational roles in that community.

## 2) Labels (2–4 labels required)

We define three mutually-exclusive labels. Each label is one sentence, followed by two clear examples and one uncertain example.

- `analysis`: The post makes a structured argument or explanation supported by specific, verifiable evidence (stats, game situations, historical comparisons). Example 1: "LeBron's playoff TS% drops 4 points against top-5 defenses this season — that's why defenses key him in pick-and-rolls." Example 2: "The Rockets' rotational depth explains their late-season collapse: their 2nd-unit defensive rating is 15 points worse than their starters' minutes." Uncertain example: "His playoff win rate vs top seeds is below .500." (Decision rule: if the post cites the stat to support an argument, label `analysis`; if the stat is decorative and the writing is mainly an accusation, treat as `hot_take`.)

- `hot_take`: A bold, subjective claim stated confidently with little or no verifiable evidence provided. The post asserts rather than argues, and the intent is to provoke or stake a strong opinion. Example 1: "Curry is cooked, not the same anymore — Warriors should trade him." Example 2: "Player X will never be an All-Star, peak is overrated." Uncertain example: "He was the worst playoff performer tonight." (Decision rule: if specific evidence is provided that would still support the claim when isolated, it's `analysis`; otherwise `hot_take`.)

- `reaction`: Immediate, emotional responses to events (celebration, anger, short-form commentary) with little to no argumentative structure. Example 1: "That dunk just made my night — insane!" Example 2: "Sigh. Not again…" Uncertain example: "I'm upset about the call, refs are biased." (Decision rule: if the post expands into evidence or a structured complaint, it could be `analysis`; if it's mostly emotional, label `reaction`.)

Mutual exclusivity check: most posts can be placed into exactly one of these labels using the decision rules above; when ambiguous, we resolve using the short decision rules under each uncertain example.

## 3) Hard edge cases

- One-stat posts: short posts that include a single statistic to justify a strong claim (e.g., "His playoff win rate vs top seeds is below .500"). Decision rule: if the statistic is used as a genuine supporting premise for an argument and the author would still make the same claim with the stat as the core evidence, label `analysis`. If the stat is decorative or cherry-picked to sensationalize, label `hot_take`.
- Sarcastic/ironic posts: posts that appear to be emotional but actually summarize an argument. Decision rule: prefer `analysis` only if the post contains verifiable evidence or explicit reasoning; otherwise prefer `reaction`.
- Short reaction that contains an implicit claim: if the implied claim is central and measurable, treat per the evidence rule; otherwise `reaction`.

We will add three documented edge-case examples from annotation (Milestone 3) to this section.

## 4) Data collection plan

- Source: public posts from r/nba (Reddit), collected manually to ensure each example is public and not behind authentication. We will collect via copy-paste to avoid scraping complexity and to keep the focus on annotation quality.
- Target size: 200 labeled examples (minimum). Distribution target: roughly balanced across labels (±10%). Initial plan: 70 `analysis`, 65 `hot_take`, 65 `reaction` (200 total). If after 200 examples a label accounts for >70% of the dataset, we will collect additional examples targeted at underrepresented labels until balance is acceptable.
- Columns: CSV with columns `text`, `label`, `notes`. `notes` will record why a post was difficult or any boundary decisions.

## 5) Evaluation metrics

- Primary metrics: per-class Precision, Recall, F1, and macro-averaged F1. Reason: class-level F1 shows how well the model learns each distinction and avoids masking poor per-class performance with a high overall accuracy on an imbalanced dataset.
- Secondary: overall Accuracy (for a quick baseline comparison) and confusion matrix (to show which labels are confused and in which direction).
- Additional checks: percent of unparseable LLM baseline responses (for the zero-shot baseline), and examples sampled from each confusion cell for qualitative analysis.

## 6) Definition of success

- Quantitative success: macro F1 ≥ 0.70 on the held-out test set (15%). This threshold indicates the model is capturing distinctions beyond naive chance on a 3-class task. As a softer baseline: fine-tuned model should improve macro F1 by at least +0.10 over the zero-shot LLM baseline.
- Qualitative success: the confusion matrix shows most errors concentrated between a single pair of labels (e.g., `analysis` vs `hot_take`), and the README documents 3 human-inspected failure cases with actionable label definition fixes.

## AI Tool Plan

- Label stress-testing: I will give the label definitions and edge-case rules to an LLM and ask it to generate 5–10 synthetic ambiguous posts for each pair of labels. If the LLM creates posts we cannot consistently classify, we will tighten definitions and re-run stress-tests. This helps surface unclear boundaries before labeling 200 examples.
- Annotation assistance: optional pre-labeling with an LLM is allowed but every pre-labeled example will be read and corrected by a human annotator. We will record in `notes` which examples were pre-labeled and corrected.
- Failure analysis: after evaluation, provide the list of wrong predictions (or sampled wrongs) to an LLM and ask for patterns (e.g., lexical cues, punctuation, class-conditional errors). Use the LLM findings as hypotheses and manually verify at least 10 examples per hypothesized pattern.

## Next steps (Milestone mapping)

1. Finalize these labels and decision rules (this document).  (Done)
2. Collect and annotate 200 examples into `labeled_dataset.csv` (Milestone 3).  (Next)
3. Run the Colab notebook: baseline (Section 5), fine-tune (Section 3), evaluate (Section 4), comparison & export (Section 6). Download `evaluation_results.json` and `confusion_matrix.png` and commit.  (Later)
4. Write the README results section and push the repo to GitHub.

---

End of planning.md
