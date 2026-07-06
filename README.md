# Emission-GPT Supplementary Data: Evaluating LLMs with RAG for Atmospheric Emission Knowledge

This repository contains the supplementary datasets for the manuscript **"Emission-GPT: Enhancing Large Language Models with Retrieval-Augmented Generation for Atmospheric Emission Assessment"**. The data supports the automatic evaluation, expert evaluation, and emission-factor baseline analysis presented in the study.

---

## Data Overview

The supplementary data consists of six CSV files:

| File | Description | Rows |
|------|-------------|------|
| `automatic_evaluation_benckmark.csv` | The full question-level benchmark with model responses | 239 |
| `ef_expert_baseline.csv` | Emission factor (EF) calculation tasks used as expert baseline | 10 |
| `expert_evaluation.csv` | Expert-panel evaluation of model-generated answers | 150 |
| `rag_summary.csv` | Aggregated automatic evaluation metrics (overall, per model) | 12 |
| `rag_summary_by_challenge.csv` | Automatic evaluation metrics broken down by challenge type | 72 |
| `rag_summary_by_difficulty.csv` | Automatic evaluation metrics broken down by difficulty level | 36 |

---

## File Descriptions

### 1. `automatic_evaluation_benckmark.csv`

The core benchmark file containing 239 questions with reference answers and model-generated responses under both **baseline** (no retrieval) and **RAG** (retrieval-augmented generation) settings.

#### Fields

| Field | Type | Description |
|-------|------|-------------|
| `ID` | integer | Unique question identifier (1–239) |
| `question` | text | The question posed to the model |
| `answer` | text | The reference / ground-truth answer |
| `domain` | categorical | Subject domain of the question (see **Domains** below) |
| `difficulty_id` | categorical | Difficulty level: `simple`, `medium`, or `hard` |
| `difficulty` | categorical | Fine-grained cognitive difficulty label (see **Difficulty Labels** below) |
| `challenge_type` | categorical | Real-world challenge category (only populated for the 40 `real-world-challenges` source questions; empty for the remaining 199 questions). See **Challenge Types** below |
| `question_type` | categorical | Format of the question (see **Question Types** below) |
| `source` | categorical | Origin of the question: `expert-curated`, `OneAtmos-Bench`, or `real-world-challenges` |
| `{Model}_baseline` | text | Model response under the baseline (parametric knowledge only) setting |
| `{Model}_RAG` | text | Model response under the RAG setting |
| `{Model}_RAG_contexts` | text | The retrieved context passages fed to the model under the RAG setting |

The model columns follow the naming pattern `{Model}_baseline`, `{Model}_RAG`, and `{Model}_RAG_contexts`. Six models were evaluated:

- **GPT-5.5**
- **DeepSeek-V4-Pro**
- **Qwen-3.6-Flash**
- **GLM-5.1**
- **Gemini-3.5-Flash**
- **Claude-4.6-Sonnet**

#### Field Value Catalogue

**Domains** (9 values):
`AIR QUALITY MODEL`, `ATMOSPHERIC CHEMISTRY`, `ATMOSPHERIC MERCURY`, `CLIMATE`, `EMISSION FUNDAMENTALS`, `EMISSION INVENTORY`, `EMISSION MEASUREMENT`, `EMISSION STANDARDS`, `POLLUTION CONTROL`

**Difficulty Levels** (`difficulty_id`, 3 values):

| Level | Count | Description |
|-------|-------|-------------|
| `simple` | 76 | Straightforward factual recall or basic computation |
| `medium` | 65 | Multi-step reasoning or cross-concept integration |
| `hard` | 98 | Complex analysis, uncertainty reasoning, or real-world scenario |

**Difficulty Labels** (`difficulty`, 7 values):
`Basic Concept Retrieval`, `Challenge Identification & Uncertainty Analysis`, `Knowledge Verification`, `Mechanism & Principle Understanding`, `Method Comparison & Evaluation`, `Numerical Computation & Quantification`, `Real-World Challenge Scenarios`

The mapping between `difficulty_id` and `difficulty` is many-to-many. For example, `hard` questions may be labelled as `Numerical Computation & Quantification`, `Real-World Challenge Scenarios`, etc.

**Question Types** (6 values):
`Definition Query`, `Explanation`, `Comparison`, `Calculation`, `True/False`, `Scenario Analysis`

**Sources** (3 values):

| Source | Count | Description |
|--------|-------|-------------|
| `expert-curated` | 60 | Questions authored by domain experts |
| `OneAtmos-Bench` | 139 | Questions drawn from the OneAtmos benchmark suite |
| `real-world-challenges` | 40 | Questions derived from real-world emission challenge scenarios |

**Challenge Types** (6 values, only for `real-world-challenges` source):
`Conflicting Standards`, `Cross-domain Integration`, `Edge Cases`, `Incomplete Parameters / Metadata`, `Region/Technology-Specific Constraints`, `Regulatory Ambiguities`

---

### 2. `ef_expert_baseline.csv`

Ten emission-factor (EF) calculation tasks designed as an expert-level baseline. Each task describes an industrial or transportation activity; the expert is expected to compute the annual pollutant emission using the formula **E = A × EF** (Activity data × Emission Factor). The actual emission factor values are hidden from experts during the test and revealed afterwards for scoring.

#### Fields

| Field | Type | Description |
|-------|------|-------------|
| `Task_id` | integer | Task identifier (1–10) |
| `Task` | text | Description of the emission calculation task |
| `Activity Level` | text | The activity data (e.g., annual coal consumption, vehicle mileage) |
| `Formula` | text | The calculation formula used (all tasks use `E = A × EF`) |
| `Emission Factor (invisible by experts)` | text | The ground-truth emission factor value with units, concealed from experts during evaluation |

#### Task Coverage

The 10 tasks span diverse source categories including coal-fired power plants, natural gas boilers, cement production, diesel trucks, biomass burning, municipal waste landfill, agricultural vehicles, industrial boilers, ceramic factories, and agricultural soil fertilizer application. Pollutants covered include CO, NOx, PM₂.₅, VOCs, BC, SO₂, and NH₃.

---

### 3. `expert_evaluation.csv`

Results from a panel of **5 domain experts** who evaluated model-generated answers. Each expert scored 30 answer instances across 5 questions and 6 model variants (3 models × 2 settings: baseline and Emission-GPT/RAG).

#### Fields

| Field | Type | Description |
|-------|------|-------------|
| `expert_id` | integer | Expert panelist identifier (1–5) |
| `question_id` | integer | Question identifier (1–5) |
| `model (invisible for experts)` | categorical | The model and setting that generated the answer. Experts were blinded to this information. Values: `GPT-5.5_baseline`, `GPT-5.5_Emission-GPT`, `DeepSeek-V4-Pro_baseline`, `DeepSeek-V4-Pro_Emission-GPT`, `Qwen-3.6-Flash_baseline`, `Qwen-3.6-Flash_Emission-GPT` |
| `question` | text | The question text |
| `answer` | text | The model-generated answer being evaluated |
| `Relevance` | integer (1–5) | How relevant the answer is to the question (1 = irrelevant, 5 = highly relevant) |
| `Accuracy` | integer (1–5) | Factual correctness of the answer (1 = very inaccurate, 5 = fully accurate) |
| `Specification` | integer (2–5) | Quality of answer formatting, structure, and completeness (2 = poor, 5 = excellent). Note: no score of 1 was given |
| `Citation` | integer (1–5) | Quality and appropriateness of cited references (1 = no/irrelevant citations, 5 = well-cited) |
| `Overall` | integer (2–5) | Holistic quality rating (2 = poor, 5 = excellent). Note: no score of 1 was given |

#### Data Structure

- 5 experts × 5 questions × 6 model variants = **150 rows**
- Each expert evaluated all 5 questions across all 6 model variants (30 evaluations per expert)

---

### 4. `rag_summary.csv`

Aggregated automatic evaluation metrics for each of the 6 models under both baseline and RAG settings (12 rows total). Metrics were computed using the RAGAS evaluation framework.

#### Fields

| Field | Type | Description |
|-------|------|-------------|
| `model` | categorical | Model name |
| `type` | categorical | `baseline` or `rag` |
| `AnswerRelevancy` | float [0, 1] | How well the answer addresses the question |
| `AnswerRelevancy_CI` | float | 95% confidence interval half-width for AnswerRelevancy |
| `AnswerSimilarity` | float [0, 1] | Semantic similarity between the answer and the reference |
| `AnswerSimilarity_CI` | float | 95% confidence interval half-width for AnswerSimilarity |
| `Faithfulness` | float [0, 1] | Factual consistency of the answer with respect to retrieved contexts |
| `Faithfulness_CI` | float | 95% confidence interval half-width for Faithfulness |
| `ContextRelevance` | float [0, 1] or empty | How relevant the retrieved contexts are to the question. **Only populated for RAG rows**; baseline rows have no context metrics |
| `ContextRelevance_CI` | float | Confidence interval half-width |
| `ContextPrecision` | float [0, 1] or empty | Precision of retrieved contexts against ground-truth. RAG rows only |
| `ContextPrecision_CI` | float | Confidence interval half-width |
| `ContextRecall` | float [0, 1] or empty | Recall of ground-truth information in retrieved contexts. RAG rows only |
| `ContextRecall_CI` | float | Confidence interval half-width |
| `Hallucination` | float [0, 1] | Proportion of generated content not supported by contexts (lower is better) |
| `Hallucination_CI` | float | 95% confidence interval half-width for Hallucination |

#### Observed Metric Ranges (across all 12 rows)

| Metric | Min | Max |
|--------|-----|-----|
| AnswerRelevancy | 0.4837 | 0.8176 |
| AnswerSimilarity | 0.6220 | 0.7965 |
| Faithfulness | 0.5100 | 0.9107 |
| ContextRelevance | 0.5342 | 0.7095 |
| ContextPrecision | 0.8411 | 0.8932 |
| ContextRecall | 0.7106 | 0.7879 |
| Hallucination | 0.0166 | 0.4979 |

---

### 5. `rag_summary_by_challenge.csv`

Automatic evaluation metrics disaggregated by **challenge type** for each model and setting combination. Only the 40 questions sourced from `real-world-challenges` are included.

#### Fields

Same as `rag_summary.csv`, with the addition of:

| Field | Type | Description |
|-------|------|-------------|
| `challenge_type` | categorical | One of: `Conflicting Standards`, `Cross-domain Integration`, `Edge Cases`, `Incomplete Parameters / Metadata`, `Region/Technology-Specific Constraints`, `Regulatory Ambiguities` |

#### Data Structure

6 models × 2 types (baseline/rag) × 6 challenge types = **72 rows**

---

### 6. `rag_summary_by_difficulty.csv`

Automatic evaluation metrics disaggregated by **difficulty level** for each model and setting combination.

#### Fields

Same as `rag_summary.csv`, with the addition of:

| Field | Type | Description |
|-------|------|-------------|
| `difficulty` | categorical | One of: `simple`, `medium`, `hard` |

#### Data Structure

6 models × 2 types (baseline/rag) × 3 difficulty levels = **36 rows**

---

## Notes

- All CSV files use UTF-8 encoding (with BOM).
- In `automatic_evaluation_benckmark.csv`, model response columns may contain multi-line Markdown text enclosed in quotes.
- Context-related metrics (`ContextRelevance`, `ContextPrecision`, `ContextRecall`) are only available for RAG-type evaluations, since baseline models do not retrieve external contexts. Accordingly, these fields are empty for baseline rows.
- The `challenge_type` field in the benchmark file is only populated for questions from the `real-world-challenges` source (40 out of 239 questions).
- Expert evaluation scores use a 1–5 Likert scale. In practice, the minimum observed score was 1 (for Relevance, Accuracy, and Citation) and 2 (for Specification and Overall).
- The emission factor values in `ef_expert_baseline.csv` are labelled "invisible by experts" because they were withheld during the expert evaluation exercise and revealed only for post-hoc scoring.
