# ai201-project3-takemeter
TakeMeter is a fine‑tuned DistilBERT classifier built for CodePath AI201. It analyzes discourse quality in an online community using a custom label taxonomy, a 200+ post dataset, and a zero‑shot Groq baseline. Includes metrics, confusion matrix, and analysis.


---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Community Choice and Reasoning](#community-choice-and-reasoning)
3. [Label Taxonomy](#label-taxonomy)
4. [Hard Edge Cases](#hard-edge-cases)
5. [Data Collection](#data-collection)
6. [Fine-Tuning Approach](#fine-tuning-approach)
7. [Baseline Description](#baseline-description)
8. [Evaluation Report](#evaluation-report)
9. [Wrong Prediction Analysis](#wrong-prediction-analysis)
10. [Sample Classifications](#sample-classifications)
11. [Reflection](#reflection-what-the-model-captured-vs-what-was-intended)
12. [Spec Reflection](#spec-reflection)
13. [AI Usage Section](#ai-usage-section)
14. [Stretch Features](#stretch-features)
15. [Deployed Interface](#deployed-interface-stretch-feature)
16. [How to Run](#how-to-run)
17. [Repository Structure](#repository-structure)
18. [Conclusion](#conclusion)
19. [References](#references)

---

# TakeMeter: Soccer Discourse Classifier

## Project Overview

TakeMeter is a text classifier that evaluates discourse quality in r/soccer comment sections. This project fine-tunes `distilbert-base-uncased` to classify comments into three categories: **analysis**, **hot_take**, and **reaction**. The goal is to distinguish between substantive football analysis, bold opinions without evidence, and emotional or humorous reactions.

---

## Community Choice and Reasoning

I chose **r/soccer** as my online community because it features diverse discourse ranging from tactical analysis and statistical breakdowns to emotional reactions and hot takes. The comment sections of post-match threads are particularly rich for this task, as fans debate performances, share statistics, and express opinions about matches, managers, and players.

**Why r/soccer is a good fit:**
- **Varied discourse quality**: Comments range from detailed statistical analysis (xG, possession, pass completion) to pure emotional reactions
- **Community norms**: The community itself distinguishes between "serious discussion" and "banter" or "reactionary takes"
- **Rich source material**: Post-match threads generate hundreds of comments with diverse perspectives
- **Accessibility**: All content is public and easy to collect

These distinctions matter to r/soccer users because they frequently call out "reactionary" takes, upvote detailed analysis, and mock "hot take" artists in the community.

---

## Label Taxonomy

Three mutually exclusive labels were defined:

### Label 1: Analysis

**Definition:** A comment that provides specific, verifiable evidence (statistics, tactical observations, historical data, or structural reasoning) to support its claim. The argument stands on its own even without emotional framing.

**Example 1:** *"0.69(lol)xG with 75% possession"*
- **Why:** Cites specific xG and possession stats to evaluate Portugal's ineffective possession-based performance. The numbers speak for themselves.

**Example 2:** *"I don't understand why the players never really attempted to shoot outside the box, we have the players capable of that but insisted in ground passing it into the middle of the box..."*
- **Why:** Offers tactical observation with specific reasoning about team strategy and player capabilities.

**Example 3:** *"Ronaldo hasn't scored a non-penalty goal in a major competition since June 19, 2021."*
- **Why:** Provides specific, verifiable historical data to support a point about Ronaldo's performance.

### Label 2: Hot Take

**Definition:** A bold, confident opinion stated with little to no supporting evidence. The comment asserts a strong claim but doesn't back it up with specific reasoning or data.

**Example 1:** *"Martinez is one of the worst managers I've seen"*
- **Why:** Strong claim with no supporting evidence, statistics, or tactical reasoning. Pure assertion.

**Example 2:** *"Their entire attack is garbage. Bruno and Ronaldo were terrible"*
- **Why:** Bold assertion without statistical or tactical details to support the claim.

**Example 3:** *"Portugals FA deserve the gulag for appointing Martinez. The truest Bald Fraud in the game"*
- **Why:** Strong opinion with humorous framing but no evidence or reasoning provided.

### Label 3: Reaction

**Definition:** An immediate emotional or humorous response to an event (match result, goal, performance). Expresses feeling, hype, or sarcasm without substantive argument.

**Example 1:** *"They played like 2018 Spain today"*
- **Why:** Quick comparison for effect, not a developed argument. Expresses disappointment through a cultural reference.

**Example 2:** *"Ronaldo: 'team?'"*
- **Why:** A sarcastic one-liner making fun of Ronaldo's perceived selfishness. Pure joke with no substance.

**Example 3:** *"Iberia 🤝 possession with no end product"*
- **Why:** Meme-format comment identifying a pattern but purely for humor/emotional response rather than substantive analysis.

---

## Hard Edge Cases

### Edge Case 1: Comments with a single statistic but emotional framing

**Example:** *"0.69 xG with 75% possession? LMAO Martinez is useless"*

This comment cites specific stats (0.69 xG, 75% possession) but the framing is clearly emotional/opinionated ("LMAO Martinez is useless").

**Decision Rule:** If the statistic is central to making a substantive point and would stand as evidence even without the opinion framing, label it **analysis**. If the data is cherry-picked, used sarcastically, or just decoration around a pre-existing opinion, label it **hot_take**.

**Ask:** "If I removed the emotional language, would this still be a reasonable argument?"
- If YES → analysis
- If NO → hot_take

### Edge Case 2: Meme/joke format comments that contain a factual observation

**Example:** *"Iberia 🤝 possession with no end product"*

This comment identifies a pattern (Iberian teams with possession but no goals) in meme format.

**Decision Rule:** If the comment identifies a pattern with specific context or implied evidence, label it **analysis**. If it's purely a joke or emotional response with no substantive point, label it **reaction**.

**Ask:** "Is the primary purpose to inform/argue or to entertain/express emotion?"
- If inform/argue → analysis
- If entertain/express → reaction

### Edge Case 3: Comments that present data but are primarily defensive or dismissive

**Example:** *"So one clip on the entire match and its framed as sorry team needs to score not you. It's a joke. The entire team has an issue when they dont create a single big chance with that midfield - bruno created more chances in epl with a much worse midfield."*

This comment cites data about Bruno's chance creation but is primarily defending Ronaldo against criticism.

**Decision Rule:** If the comment uses data to make a reasoned counter-argument that would make sense even if you removed the defensive tone, label it **analysis**. If the data is just a shield for an emotional defense ("it's a joke"), label it **hot_take**.

---

## Data Collection

### Source

I collected comments from **r/soccer post-match threads** and **discussion posts** about matches, player performances, and manager decisions. Specifically:
- Post-match threads after major matches (international friendlies, World Cup qualifiers, Premier League, Champions League)
- Discussion posts about specific players (Ronaldo, Messi, etc.) and managers
- Tactical analysis posts with comment sections

### Labeling Process

Data was collected manually by copy-pasting comments into a CSV file with columns: `id`, `text`, `label`, and `notes`. Each comment was read carefully and labeled using the definitions and edge case rules from the planning document. I used an LLM to pre-label 50 examples, which I then reviewed and corrected myself to speed up the annotation process while maintaining quality.

**Dataset Link:**  [Excel Dataset]
https://merced-my.sharepoint.com/:x:/g/personal/fperezrugama_ucmerced_edu/IQAEP6GlBJEvQ5Og9xS0GQppAeTMLUPk1LCpmr1cnsloNEo?e=aXtYOw

The dataset can also be found in this repo under: `ai201p3_takeMeter_data(Sheet1).csv`

### Label Distribution

| Label | Count | Percentage |
|-------|-------|------------|
| **analysis** | ~100 | 41% |
| **reaction** | ~93 | 38% |
| **hot_take** | ~50 | 21% |
| **Total** | **243** | **100%** |

No single label exceeds 70% of the dataset. The dataset was split into train (70%), validation (15%), and test (15%) sets.

### Difficult-to-Label Examples

| Comment | Labels Considered | Final Decision | Reasoning |
|---------|-------------------|----------------|-----------|
| *"0.69 xG with 75% possession? LMAO Martinez is useless"* | analysis vs hot_take | hot_take | Stats are decorative around an emotional opinion. The primary purpose is to attack Martinez, not analyze stats. |
| *"Iberia 🤝 possession with no end product"* | analysis vs reaction | reaction | Meme format with no substantive argument. The primary purpose is humor. |
| *"Everytime they got remotely close to the box he lifted his arm and ran towards goal even if he was in a bad position. Like, bro chill"* | analysis vs hot_take | hot_take | Observation is presented as opinion with no evidence. "Bro chill" makes it a dismissive hot take. |

---

## Fine-Tuning Approach

### Base Model

- **Model:** `distilbert-base-uncased`
- **Platform:** Google Colab with T4 GPU
- **Training Time:** ~10-15 minutes
- **Dataset Size:** 243 examples

### Hyperparameters

| Parameter | Value | Reasoning |
|-----------|-------|-----------|
| **Epochs** | 3 | Standard for small datasets; prevents overfitting |
| **Learning Rate** | 2e-5 | Standard starting point for BERT-family models |
| **Batch Size** | 16 | Fits comfortably on T4 GPU |
| **Weight Decay** | 0.01 | Prevents overfitting |
| **Warmup Steps** | 50 | Gradual learning rate increase for stability |

### Training Performance

| Epoch | Training Loss | Validation Loss | Validation Accuracy |
|-------|--------------|-----------------|---------------------|
| 1 | 1.1097 | 1.0977 | 38.9% |
| 2 | 1.0944 | 1.0719 | 38.9% |
| 3 | 1.0700 | 1.0312 | 50.0% |

**Key Insight:** The validation accuracy peaked at only 50.0%, indicating the model struggled to learn the distinctions between labels. This suggests the task may be too nuanced for DistilBERT with limited training data.

---

## Baseline Description

### Zero-Shot Baseline (Groq)

The zero-shot baseline used Groq's `llama-3.3-70b-versatile` with **no task-specific training**. The prompt included label definitions and examples, instructing the model to output only the label name.

**Prompt Used:**

```
You are a classifier for soccer/football discourse on r/soccer. Your task is to classify comments and posts into ONE of three categories.

LABELS AND DEFINITIONS:
1. analysis - A comment that provides specific, verifiable evidence (statistics, tactical observations, historical data, or structural reasoning) to support its claim. The argument stands on its own even without emotional framing.
2. hot_take - A bold, confident opinion stated with little to no supporting evidence. The comment asserts a strong claim but doesn't back it up with specific reasoning or data.
3. reaction - An immediate emotional or humorous response to an event (match result, goal, performance). Expresses feeling, hype, or sarcasm without substantive argument.

RULES:
- Output ONLY one of these exact words: analysis, hot_take, reaction
- Do NOT output any additional text, explanation, or punctuation
- Choose the label that best fits the comment
```

**Results Collection:** The notebook classified every example in the test set (37 examples) using the Groq API with a 0.1s delay between requests to respect free-tier limits. All responses were parseable (37/37).

---

## Evaluation Report

### Results Comparison

| Model | Accuracy |
|-------|----------|
| **Zero-shot Baseline (Groq)** | **73.0%** |
| **Fine-tuned DistilBERT** | **45.9%** |

**Fine-tuning regression: -27.1%**

The fine-tuned model performed significantly worse than the zero-shot baseline, indicating that fine-tuning failed to learn meaningful distinctions.

### Per-Class Metrics (Fine-Tuned Model)

| Label | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| **analysis** | 0.44 | 1.00 | 0.61 | 14 |
| **hot_take** | 0.00 | 0.00 | 0.00 | 8 |
| **reaction** | 0.60 | 0.20 | 0.30 | 15 |
| **Accuracy** | | | | **45.9%** |

### Per-Class Metrics (Baseline - Groq)

| Label | Precision | Recall | F1-Score | Support |
|-------|-----------|--------|----------|---------|
| **analysis** | 0.82 | 0.64 | 0.72 | 14 |
| **hot_take** | 0.67 | 0.50 | 0.57 | 8 |
| **reaction** | 0.70 | 0.93 | 0.80 | 15 |
| **Accuracy** | | | | **73.0%** |

### Confusion Matrix (Fine-Tuned Model)

```
True Label      analysis  hot_take  reaction
analysis           14         0         0
hot_take            6         0         2
reaction           12         0         3
```

**Interpretation:** The model predicted **analysis** for 32 out of 37 test examples (86% of predictions), completely failing to identify hot_take examples. The model never predicted "hot_take" even once.

---

## Wrong Prediction Analysis

### Example 1: hot_take → analysis (Confidence: 0.41)

**Text:** *"I doubt Infantino has done a single thing to try and help anyone that has a net worth under $1 billion."*

| True Label | Predicted | Confidence |
|------------|-----------|------------|
| hot_take | analysis | 0.41 |

**Why Did It Fail?** This comment expresses a strong opinion about FIFA's president with no evidence. However, the model saw a reasoned-sounding statement and classified it as analysis. The comment's structure (a complete sentence with reasoning) confused the model.

**Which labels are being confused?** hot_take → analysis

**Why is that boundary hard?** The model associates "reasoning structure" with "analysis." Hot takes that contain complete sentences and coherent reasoning get misclassified.

**Is this a labeling problem or a data problem?** This is a data problem. The model needs more examples of hot takes that contain reasoning structure but lack evidence. It's also a label nuance issue - the distinction between "reasoning with evidence" and "reasoning without evidence" is subtle.

**What would need to change?** More examples of hot takes that look like analysis on the surface (complete sentences, coherent reasoning) but lack evidence. Clearer differentiation in the training data.

---

### Example 2: hot_take → analysis (Confidence: 0.41)

**Text:** *"Why not hire a proper manager if you are trying to win it?"*

| True Label | Predicted | Confidence |
|------------|-----------|------------|
| hot_take | analysis | 0.41 |

**Why Did It Fail?** This is a rhetorical question implying the current manager is inadequate. However, it's presented as a complete, logical-sounding question, which the model interpreted as analysis. The comment lacks evidence for why the manager is not "proper."

**Which labels are being confused?** hot_take → analysis

**Why is that boundary hard?** The question format makes it sound analytical, but it's actually a dismissive hot take. The model doesn't distinguish between "asking a genuine analytical question" and "making a rhetorical dismissive statement."

**What would need to change?** More examples of rhetorical questions that are actually hot takes. The model needs to learn that questions can be dismissive/opinionated too.

---

### Example 3: reaction → analysis (Confidence: 0.43)

**Text:** *"Post-Match Thread: Canada 6-0 Qatar | FIFA World Cup, Group B Canada 6 - 0 Qatar"*

| True Label | Predicted | Confidence |
|------------|-----------|------------|
| reaction | analysis | 0.43 |

**Why Did It Fail?** This is a simple factual announcement of a match result - a post-match thread title. There's no analysis, just reporting the score. The model misclassified it as analysis because it contains numbers and looks like a complete, informative statement.

**Which labels are being confused?** reaction → analysis

**Why is that boundary hard?** The model associates "informative content" with "analysis." Simple factual reporting gets classified as analysis because it contains information.

**What would need to change?** More examples of factual announcements labeled as reaction. The model needs to learn the difference between "reporting facts" and "analyzing facts."

---

## Sample Classifications

| Text | True Label | Predicted Label | Confidence | Correct? |
|------|------------|-----------------|------------|----------|
| *"Turkey averaging 72% possession across the first two games and have no points or goals"* | analysis | analysis | 0.62 | ✅ |
| *"62 shots 13 on target in 2 matches and zero goals lol. I never want to hear Turkey called dark horses again"* | analysis | analysis | 0.58 | ✅ |
| *"No goals against Australia and Paraguay in 180+ minutes... is crazy work"* | analysis | analysis | 0.58 | ✅ |
| *"That manager needs to be fired to the moon, what kinda attacking set up was that"* | hot_take | reaction | 0.38 | ❌ |
| *"USA World Cup goals are literally shaking Seattle's Lumen Field"* | reaction | analysis | 0.39 | ❌ |

**Correct Prediction Explained:**
The first example, *"Turkey averaging 72% possession across the first two games and have no points or goals,"* was correctly classified as **analysis**. This is because it provides specific, verifiable statistics (72% possession, no points or goals) to support its claim about Turkey's ineffective performance. The evidence stands on its own without emotional framing, making it a clear analysis example.

---

## Reflection: What the Model Captured vs. What Was Intended

### What I Intended:
The model should distinguish between:
- **Analysis:** Evidence-based arguments with stats or tactical reasoning
- **Hot Take:** Bold opinions without evidence
- **Reaction:** Emotional or humorous responses

### What the Model Actually Captured:
The model learned that:
- **Any complete sentence with structure = analysis** (86% of predictions were "analysis")
- **Any factual information = analysis** (match threads, quotes, announcements)
- **Hot takes are invisible** - the model never predicted "hot_take" even once
- **Reactions are sometimes caught**, but often misclassified as analysis

### Why This Gap Exists:

1. **Class Imbalance**: With only ~50 hot_take examples (21%), the model didn't see enough hot takes to learn their patterns. It defaulted to the most common class (analysis).

2. **Label Nuance**: The boundary between analysis and hot_take is subtle. Both can contain reasoning. DistilBERT (66M parameters) couldn't learn this distinction with limited data.

3. **Data Quality Issues**: Upon reviewing my labeling, I noticed some inconsistencies. For example, comments like *"Turkey are so bad. Like cartoonishly bad"* could reasonably be labeled as either hot_take or reaction. My father also noted that several examples I labeled as hot_take seemed more like reactions to him:
   - ID 134: *"Turkey are so bad. Like cartoonishly bad"* → He saw as reaction
   - ID 126: *"Turkey -->> Dark horse ❌️ horse shit ✅️..."* → He saw as reaction
   - ID 111: *"After Messi yesterday there was never a chance in hell Ronaldo would be even the slightest bit unselfish"* → He saw as reaction

   This suggests that **hot_takes and reactions are often very similar** in soccer discourse. Fans are emotional when writing comments, making it difficult to distinguish between "bold opinion without evidence" and "emotional reaction."

4. **The Nature of Soccer Discourse**: Soccer fans are deeply passionate and emotional. Even when they're trying to make a point, there's often an emotional undertone. This makes the hot_take vs reaction boundary naturally blurry.

5. **Model Size**: DistilBERT is small compared to the baseline Groq model (66M vs 70B parameters). The baseline has vastly more world knowledge, which helps with nuanced classification.

### What the Model Overfit To:
The model overfit to the **surface-level features** of analysis examples: complete sentences, presence of numbers/statistics, and informative content. It failed to learn the **deeper distinction** between "reasoning with evidence" and "reasoning without evidence."

---

## Spec Reflection

### How the Spec Helped:
The project spec forced me to define clear labels with examples before collecting data. This prevented me from labeling inconsistently and gave me a framework to apply consistently across 243 examples. The spec also emphasized documenting difficult cases, which helped me refine my labeling criteria over time. The requirement to run a baseline comparison (Groq) before fine-tuning was especially valuable - it revealed that the zero-shot model was already performing well (73.0%), giving me a realistic benchmark to compare against.

### How Implementation Diverged:
The spec assumed fine-tuning would improve performance. In my case, the zero-shot baseline (Groq) significantly outperformed the fine-tuned model (73.0% vs 45.9%). I diverged by spending extra time adding more hot_take examples to try to improve the model, but this did not resolve the underlying issue. The model's failure highlighted limitations of small datasets and small models for nuanced classification tasks. If I were to do this again, I would either:
1. Use a larger model (e.g., BERT-base or RoBERTa)
2. Collect significantly more data (500+ examples)
3. Use the zero-shot Groq model as the primary classifier rather than fine-tuning a small model

---

## AI Usage Section

### Instance 1: Label Stress-Testing

**What I Directed:** I gave Claude my label definitions and edge case rules, asking it to generate 10 borderline examples to test my taxonomy before annotating.

**What It Produced:** Claude generated several examples that sat at the boundary between labels, including comments with single statistics but emotional framing, and meme-format comments with valid observations.

**What I Changed/Overrode:** I refined my edge case rules based on Claude's examples. For instance, the "single statistic with emotional framing" edge case came directly from this stress-testing. I also added a clear decision rule for meme-format comments based on Claude's suggestions.

---

### Instance 2: Error Pattern Analysis

**What I Directed:** After evaluation, I fed my misclassified examples to Claude and asked it to identify patterns in the errors.

**What It Produced:** Claude identified that the model consistently misclassifies hot_takes as analysis when they contain any reasoning structure or complete sentences. It also noted that the model associates "informative content" with "analysis" regardless of whether it's actually analytical.

**What I Changed/Overrode:** I verified these patterns by re-reading the wrong predictions myself. I confirmed the pattern was accurate and used it to inform my evaluation report. Claude's analysis helped me see patterns I might have missed when reviewing cases one at a time.

---

### Annotation Assistance

**What I Directed:** I used Claude to pre-label a batch of 50 examples before reviewing them myself. I provided the label definitions and edge case rules.

**What It Produced:** Claude assigned labels to 50 comments. Some were correct, but others had to be corrected (especially borderline cases between hot_take and reaction).

**What I Changed/Overrode:** I reviewed and corrected every pre-labeled example myself. For borderline cases (e.g., comments with reasoning but no evidence), I applied my edge case rules to make the final decision. I tracked which examples were pre-labeled in my CSV with a `pre_labeled` column.

---

## Stretch Features

### Error Pattern Analysis

**Systematic Pattern Found:** The model consistently misclassifies hot_takes as analysis when they contain any form of reasoning structure or complete sentences. The model associates "reasoning" with "analysis," regardless of whether evidence is provided.

**Examples:**
- *"I doubt Infantino has done a single thing..."* → hot_take misclassified as analysis
- *"Why not hire a proper manager..."* → hot_take misclassified as analysis

**Why This Pattern Occurs:** The model learned that analysis examples tend to be longer, complete sentences with reasoning. Hot_take examples that share these surface features get misclassified. The model failed to learn the deeper distinction between "reasoning with evidence" and "reasoning without evidence."

### Confidence Calibration

**Analysis:** The model's confidence scores are **not meaningful**. Confidence scores for wrong predictions ranged from 0.34 to 0.43, barely above random chance (0.33 for 3 classes). The model is uncertain about nearly all predictions, yet it still defaults to "analysis."

| Scenario | Average Confidence |
|----------|-------------------|
| Correct predictions | 0.45 |
| Wrong predictions | 0.38 |
| Random chance | 0.33 |

**Conclusion:** The model's confidence scores are not calibrated. A 90% confident prediction does not exist - all predictions cluster around the 35-45% range. The model is consistently uncertain but makes a default choice (analysis) when unsure. Confidence scores cannot be trusted for deployment.

---

## Deployed Interface (Stretch Feature)

I built a simple web interface using **Gradio** that runs in the Colab notebook. It accepts a soccer comment and classifies it using Groq's `llama-3.3-70b-versatile` model.

### How to Run

1. Open the Colab notebook
2. Run the "Deployed Interface" section at the bottom
3. Click the Gradio link that appears (`https://90b6ccc47f759d4ed9.gradio.live`)

### Interface Features

- Enter any soccer comment in the text box
- Click "Classify" to get the predicted label
- View confidence score
- Try example comments from the dropdown

### Why Groq Instead of Fine-Tuned Model?

The fine-tuned DistilBERT model achieved only 45.9% accuracy, while Groq's zero-shot baseline achieved 73.0%. For a deployed interface, Groq provides more reliable classifications.

---

## How to Run

1. Open the Colab notebook: [TakeMeter Starter Notebook](  https://colab.research.google.com/drive/1JCTSytaXytjQzTa4t8TMbXzpTqc6n11r?usp=sharing  )
2. Set runtime to T4 GPU
3. Run Section 1: Define LABEL_MAP and upload your CSV
4. Run Section 2: Split and tokenize data
5. Run Section 5: Set Groq API key and run baseline
6. Run Section 3: Fine-tune DistilBERT
7. Run Section 4: Evaluate the fine-tuned model
8. Run Section 6: Compare results and export

---

## Repository Structure

```
ai201-project3-takemeter/
├── planning.md                          # Design document
├── README.md                            # This file
├── labeled_data.csv                     # Annotated dataset (243 examples)
├── evaluation_results.json              # Evaluation metrics
├── confusion_matrix.png                 # Confusion matrix
└── interface_app.py                     # Deployed interface (stretch feature)
```

---

## Conclusion

While the fine-tuned model failed to outperform the zero-shot baseline, the project successfully demonstrated the challenges of fine-tuning small models for nuanced classification tasks with limited data. The zero-shot baseline (Groq) achieved 73.0% accuracy, demonstrating that large language models with broad knowledge can perform well on this task without fine-tuning.

**Key Lessons:**
1. **Data quality matters more than quantity** - even with 243 examples, label nuance requires careful definition
2. **Class imbalance is a major obstacle** - underrepresented classes may not be learned at all
3. **Model size matters** - DistilBERT (66M params) couldn't compete with Groq (70B params) for this task
4. **Zero-shot baselines are strong** - a good prompt with a large model can outperform small fine-tuned models
5. **Label boundaries can be blurry** - the distinction between hot_take and reaction is often subjective, making consistent labeling difficult

---

## References

- DistilBERT. (2019). DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter. *arXiv:1910.01108*.
- Groq. (2024). Groq Cloud API Documentation. https://groq.com
- Hugging Face. (2024). Transformers Documentation. https://huggingface.co/docs/transformers
- CodePath. (2024). AI201: Applications of AI Engineering - Project 3: TakeMeter. https://courses.codepath.org