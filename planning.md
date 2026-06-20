# planning.md - TakeMeter

## 1. Community

I chose r/soccer as my online community because it features diverse discourse ranging from tactical analysis and statistical breakdowns to emotional reactions and hot takes. The comment sections of post-match threads are particularly rich for this task, as fans debate performances, share statistics, and express opinions about matches, managers, and players. These distinctions matter to r/soccer users because they frequently distinguish between 'serious' discussion and 'banter' or 'reactionary' takes in their community norms.

r/soccer is ideal because discourse quality varies enormously:

Some comments provide detailed statistical analysis (xG, possession, pass completion)

Others offer tactical observations about formation, player positioning, and strategy

Many are pure emotional reactions to goals, results, or controversial moments

The community itself distinguishes between "serious discussion" and "banter" or "reactionary takes"

This variation makes it possible to define meaningful, mutually exclusive categories that reflect how community members actually evaluate discourse quality. The distinctions matter to r/soccer users because they frequently call out "reactionary" takes, upvote detailed analysis, and mock "hot take" artists in the community.

## 2. Labels

### Label 1: Analysis
Definition: A comment that provides speciic, verifiable evidence (statistics, tactical observations, historical data, or structural reasoning) to support its claim. The argument stands on its own even without emotional framing. 

**Example 1** (from r/soccer):
 "0.69(lol)xG with 75% possesion"

Why this is analysis: Cites specific xG and possession stats to evaluate Portugal's ineffective possesion-based performance. The numbers speak for themselves. 

**Example 2** (from r/soccer):
 "I don't understand why the players never really attempted to shoot outside the box, we have the players capable of that but insisted in ground passing it into the middle of the box..."

Why this is analysis: Offers tactical observation with specific reasoning about team strategy and player capabilities.

**Example 3**(from r/soccer):

 "Ronaldo hasn't scored a non-penalty goal in a major competition since June 19, 2021."

Why this is analysis: Provides specific, verifiable historical data to support a point about Ronaldo's performance.

### Label 2: hot_take
Definition: A bold, confident opinion stated with little to no supporting evidence. The comment asserts a strong claim but doesn't back it up with specific reasoning or data.

**Example 1** (from r/soccer):
 "Martinez is one of the worst managers i've seen"

Why this is a hot_take: Strong claim with no supporting evidence, statistics, or tactical reasoning. Pure assertion.

**Example 2** (from r/soccer):
 "Their entire attack is garbage. Bruno and Ronaldo were terrible"

Why this is a hot_take: Bold assertion without statistical or tactical details to support the claim.

**Example 3** (from r/soccer):
 "Portugals FA deserve the gulag for appointing Martinez. The truest Bald Fraud in the game"

Why this is a hot_take: Strong opinion with humorous framing but no evidence or reasoning provided.

### Label 3: reaction
Definition: An immediate emotional or humorous response to an event (match result, goal, performance). Expresses feeling, hype, or sarcasm without substantive argument.

**Example 1** (from r/soccer):
 "They played like 2018 spain today"

Why this is reaction: Quick comparison for effect, not a developed argument. Expresses disappointment through a cultural reference.

**Example 2** (from r/soccer):
 "Ronaldo: 'team?'"

Why this is reaction: A sarcastic one-liner making fun of Ronaldo's perceived selfishness. Pure joke with no substance.

**Example 3** (from r/soccer):
 "Iberia 🤝 possession with no end product"

Why this is reaction: Meme-format comment identifying a pattern but purely for humor/emotional response rather than substantive analysis.

## 3. Hard Edge Cases

**Edge Case 1: Comments with a single statistic but emotional framing**
- Example: "0.69 xG with 75% possession? LMAO Martinez is useless"

This comment cites specific stats (0.69 xG, 75% possession) but the framing is clearly emotional/opinionated ("LMAO Martinez is useless").

- Decision Rule: If the statistic is central to making a substantive point and would stand as evidence even without the opinion framing, label it analysis. If the data is cherry-picked, used sarcastically, or just decoration around a pre-existing opinion, label it hot_take.

Ask: "If I removed the emotional language, would this still be a reasonable argument?"

If YES → analysis
If NO → hot_take

**Edge Case 2: Meme/joke format comments that contain a factual observation**
- Example: "Iberia 🤝 possession with no end product"
This comment identifies a pattern (Iberian teams with possession but no goals) in meme format.
- Decision Rule: If the comment identifies a pattern with specific context or implied evidence, label it analysis. If it's purely a joke or emotional response with no substantive point, label it reaction.

Ask: "Is the primary purpose to inform/argue or to entertain/express emotion?"

If inform/argue → analysis
If entertain/express → reaction


**Edge Case 3: Comments that present data but are primarily defensive or dismissive**
- Example: "So one clip on the entire match and its framed as sorry team needs to score not you. It's a joke. The entire team has an issue when they dont create a single big chance with that midfield - bruno created more chances in epl with a much worse midfield."

This comment cites data about Bruno's chance creation but is primarily defending Ronaldo against criticism.

- Decision Rule: If the comment uses data to make a reasoned counter-argument that would make sense even if you removed the defensive tone, label it analysis. If the data is just a shield for an emotional defense ("it's a joke"), label it hot_take.

## 4. Data Collection Plan

I will collect comments from r/soccer post-match threads and discussion posts about matches, player performances, and manager decisions. Specifically:

* Post-match threads after major matches (international friendlies, World Cup qualifiers, Premier League, Champions League)
* Discussion posts about specific players (Ronaldo, Messi, etc.) and managers
* Tactical analysis posts with comment sections

I will target approximately 70 examples per label to maintain a balanced distribution (aiming for at least 20% per label). Total target: 210 examples (slightly above the 200 minimum).

| Label     | Target Count | Minimum |
|-----------|--------------|---------|
| analysis  | ~70          | 50      |
| hot_take  | ~70          | 50      |
| reaction  | ~70          | 50      |
| **Total** | **~210**     | **200** |

If any label falls below 50 examples after initial collection, I will:

1. Specifically search for more examples of that label (e.g., read comments on tactical analysis posts for more analysis examples)
2. Target comment sections that are likely to contain the underrepresented label
3. If still under-represented, I may need to adjust my labels or collect additional examples from other r/soccer threads

Data will be collected manually by copy-pasting comments into a CSV file with columns: text, label, and notes (for tracking difficult cases).

## 5. Evaluation Metrics

I will evaluate both the fine-tuned model and zero-shot baseline using:

* Overall accuracy - A basic measure of correct predictions

* Per-class precision, recall, and F1 - To understand which labels the model handles well vs. poorly

* Confusion matrix - To identify specific label confusions (e.g., analysis → hot_take)

These metrics matter because this is a 3-class problem where label distribution and confusion patterns reveal more than accuracy alone. For example:

* High accuracy with low recall on "analysis" would mean the model only predicts analysis when extremely confident

* High precision with low recall on "hot_take" would mean the model is conservative about labeling things as hot_takes

* A confusion matrix showing analysis ↔ hot_take confusion would tell me the boundary between these labels needs attention

Accuracy alone could hide critical issues. For example, if 70% of my dataset is "reaction," a model could achieve 70% accuracy by predicting "reaction" for everything, but it would be useless for identifying analysis or hot_take comments. Per-class metrics and confusion matrices reveal whether the model is actually learning the distinctions I care about.

## 6. Definition of Success

Success criteria:

* Minimum: Fine-tuned model achieves at least 10% higher accuracy than zero-shot baseline

* Target: All per-class F1 scores ≥ 0.65, with no single class below 0.50

* Stretch: Model correctly identifies at least 70% of "analysis" examples (the hardest class to distinguish from hot_take)

For this classifier to be genuinely useful in a real community tool (e.g., a browser extension that tags comments as analysis/hot_take/reaction), I would accept:

**Minimum acceptable performance:**

- Overall accuracy ≥ 70%
- All per-class F1 ≥ 0.55
- Fine-tuned model beats zero-shot baseline by at least 10 percentage points

**Why this threshold?**

- A 70% accuracy means the model is right 7/10 times - useful for sorting/surfacing content
- F1 ≥ 0.55 ensures no label is completely misclassified (e.g., the model isn't just predicting "reaction" for everything)
- Beating the baseline proves the fine-tuning added value beyond what a general LLM can do

**Stretch goal for "genuinely useful":**

- Accuracy ≥ 80%
- All per-class F1 ≥ 0.70
- This would make the classifier reliable enough to automatically tag posts in a community feed

These criteria are specific enough that I can objectively determine at the end whether I hit them.

## 7. AI Tool Plan

- **Label stress-testing:**
I will give Claude (or another LLM) my label definitions and edge case rules, asking it to generate 10 borderline examples to test my taxonomy before annotating. If it produces posts I can't classify cleanly, my definitions need tightening - I'll do that now, before annotating 200 examples.

What I'll do with the results:
 - Review each generated comment and attempt to label it
 - If I struggle to classify >2 comments, revise definitions
 - Add any new edge cases to Section 3
 - Document which comments were hard to classify and why

- **Annotation assistance:**

I will use an LLM to pre-label a batch of examples before reviewing them myself.

Tool: Claude or ChatGPT

Workflow:

1. Provide the LLM with my label definitions and edge case rules
2. Ask it to label 50 unlabeled comments from my collection
3. Review and correct every pre-labeled example myself
4. Track which examples were pre-labeled in a column called pre_labeled in my CSV (values: yes/no)
5. Document this in the AI usage section of my README

How I'll track pre-labeled examples:

text,label,pre_labeled,notes
"0.69(lol)xG with 75% possession",analysis,yes,"Clear analysis - stats central to point"
"Martinez is one of the worst managers i've seen",hot_take,yes,"No evidence provided"
"Iberia 🤝 possession with no end product",reaction,yes,"Meme format, no substantive argument"

- **Failure analysis:**

After evaluation, I will feed my misclassified examples (from the test set) to an LLM and ask it to identify patterns.

What I'll look for:

 - Common post characteristics among errors (length, structure, topic, language)
 - Specific label pairs that get confused (e.g., analysis ↔ hot_take)
 - Sarcasm or humor patterns that confuse the model
 - Short vs. long comments - is there a length threshold where performance drops?

How I'll verify patterns:
1. Get pattern suggestions from LLM
2. Re-read the misclassified examples myself to verify
3. If patterns are confirmed, analyze why the model struggles with them
4. Document patterns in the evaluation report (README)
5. If LLM suggests patterns I don't see, note that too (shows I critically evaluated)

## Hard Annotation Decisions (from Milestone 3)

During annotation of 210 examples, I encountered several difficult cases that required careful application of my decision rules. Below are three representative examples that illustrate the challenges of labeling real-world discourse.

### Case 1: Statistics with Emotional Framing
**Comment:** *"0.69 xG with 75% possession? LMAO Martinez is useless"*

| Label Considered | Reasoning |
|------------------|-----------|
| analysis | Contains specific stats (0.69 xG, 75% possession) |
| hot_take | Emotional framing ("LMAO Martinez is useless") |

**Decision:** hot_take

**Why:** The statistics are decorative around the emotional opinion. If you remove the emotional framing, the core argument is weak. The primary purpose is to attack Martinez, not to analyze the stats. This reinforced the rule that data must be central to the argument, not just decoration.

---

### Case 2: Meme Format with a Valid Observation
**Comment:** *"Iberia 🤝 possession with no end product"*

| Label Considered | Reasoning |
|------------------|-----------|
| analysis | Identifies a real pattern about Iberian teams |
| reaction | Meme format, humorous framing |

**Decision:** reaction

**Why:** While there's a valid observation about Iberian teams, the primary purpose is humor/meme format. The comment isn't trying to prove or argue anything - it's making a funny observation. This highlighted that presentation matters: even substantive observations can be reactions if the intent is entertainment rather than argument.

---

### Case 3: Behavioral Observation with Opinion Framing
**Comment:** *"Everytime they got remotely close to the box he lifted his arm and ran towards goal even if he was in a bad position. Like, bro chill"*

| Label Considered | Reasoning |
|------------------|-----------|
| analysis | Observes specific player behavior |
| hot_take | Opinionated framing, no evidence |

**Decision:** hot_take

**Why:** While there's a specific observation about Ronaldo's behavior, the framing is purely opinionated with no evidence. The "bro chill" indicates it's a dismissive hot take rather than a serious tactical observation. This taught me that observations alone don't make analysis - they need reasoning and evidence to support the point.

## 8. Stretch Features Plan

### Error Pattern Analysis

**Status:** ✅ Completed

I will analyze the wrong predictions from my fine-tuned model to identify systematic patterns in the errors. Based on my initial review of the confusion matrix and wrong predictions, I plan to investigate:

**What I'll look for:**
- Which label pair is most frequently confused (analysis ↔ hot_take appears to be the primary confusion)
- Common characteristics among errors (post length, use of sarcasm, presence of statistics)
- Whether the model struggles more with short or long posts
- Whether certain topics (e.g., Ronaldo vs Messi debates) confuse the model more

**Method:**
1. Extract all wrong predictions from the test set
2. Group errors by true label and predicted label
3. Re-read each error to identify common themes
4. Use Claude to surface patterns I might miss
5. Verify patterns by re-reading examples

**How I'll document:**
- Include the identified pattern in the evaluation report
- Show representative examples of the pattern
- Explain why the model struggles with this pattern
- Suggest what would need to change to fix it

---

### Confidence Calibration

**Status:** ✅ Completed

I will analyze whether the model's confidence scores are meaningful and calibrated.

**What I'll investigate:**
- Do high-confidence predictions (>70%) get it right more often than low-confidence predictions (<50%)?
- Is there a correlation between confidence and accuracy?
- Are there certain labels where confidence is consistently misleading?

**Method:**
1. Extract confidence scores for all test predictions
2. Compare confidence distribution for correct vs incorrect predictions
3. Calculate average confidence for correct and incorrect predictions
4. Analyze if any label has systematically overconfident or underconfident predictions

**How I'll document:**
- Report average confidence for correct vs incorrect predictions
- Include a table showing confidence distribution
- State whether confidence scores are meaningful or not
- Explain implications for deployment

---

### Deployed Interface


I will build a simple web interface that accepts a new post, runs it through the classifier, and displays the label and confidence.

**Planned approach:**
- Use Gradio or Streamlit for the interface
- Load the fine-tuned model from HuggingFace
- Accept text input and display predicted label + confidence score
- Show confidence as a percentage or bar

**How I'll document:**
- Include code in the repository
- Document how to run the interface in the README
- Show a screenshot of the interface in the README

---

### Inter-Annotator Reliability


I will have at least one other person label 30+ of my examples independently and report agreement rate.

**Planned approach:**
- Select 30 random examples from the dataset
- Provide label definitions and edge case rules to the second annotator
- Calculate Cohen's kappa or simple percentage agreement
- Analyze where disagreements occurred

**How I'll document:**
- Report agreement rate in the README
- Analyze 2-3 disagreements and explain why they occurred
- Discuss implications for dataset quality