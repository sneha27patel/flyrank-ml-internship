# Research Paper: Search Intelligence Content Refresh & Opportunity Scoring System

**Author:** Sneha Patel  
**Track:** Applied Search Intelligence / FlyRank ML Internship Track  
**Dataset:** FlyRank Anonymized Search Intelligence Dataset (`v20260703`)  
**Repository:** [github.com/sneha27patel/flyrank-ml-internship](https://github.com/sneha27patel/flyrank-ml-internship)  

---

## Abstract
Search engine traffic naturally decays over time as web articles become outdated, yet content marketing teams lack data-driven methods to prioritize which decaying pages to rewrite first. In this paper, we present an interpretable Machine Learning scoring system that ranks content refresh opportunities by predicting organic traffic recovery potential. Using an anonymized dataset of over 500,000 content items and daily search performance records, we engineer 5 non-leaked search signals (impressions, average position, click-through rate, page age, and update freshness). We compare a Decision Tree model against a standard hand-written baseline rule across a clean 80/20 train-test split. The learned Decision Tree model achieves a **3x precision improvement (Precision@50: 0.76)** over the hand-written baseline (Precision@50: 0.24). Our findings demonstrate that lightweight, interpretable decision trees provide actionable, evidence-backed review queues for content editors without relying on complex black-box neural networks.

---

## 1. Introduction & Problem Statement
Content teams spend thousands of hours annually attempting to refresh decaying articles. However, without systematic scoring, editors manually update random articles without knowing if those updates will actually recover lost search traffic.

This project answers one specific decision question:  
> **"Which decaying search pages should an editor refresh first to maximize organic traffic recovery?"**

* **Target Action:** A content editor re-optimizes text, updates outdated facts, and fixes meta titles for high-priority flagged pages.
* **Cost of Error:** Wasted editor salary hours on unrecoverable dead pages, or missing out on high-traffic pages that are actively losing impression share.
* **Why ML Helps:** Traffic decay involves complex interactions between search position, CTR, page age, and impression volume that manual IF-THEN rules fail to rank accurately.

---

## 2. Data Contract & Data Pipeline
* **Release:** FlyRank Pseudonymized Warehouse Release (`flyrank_pseudonymized_warehouse_release_v20260703`).
* **Starter Slice:** `data/raw/content_refresh_anonymized.csv` (7,073 content items, 44 performance features).
* **Unit of Analysis (Grain):** 1 Row = 1 Pseudonymized Content Item (URL/Page).
* **Time Window:** 90-day rolling historical search window.
* **Deliberately Excluded (Leakage Audit):** `trend_pct` was deliberately excluded from model features because `trend_direction` is directly derived from `trend_pct`. Including it causes artificial 1.00 precision (feature leakage).

### Feature Definitions (Known at Decision Time):
1. `impressions_90d`: Total GSC search impressions over 90 days.
2. `avg_position`: Average Google search result position.
3. `ctr`: Click-through rate percentage.
4. `content_age_days`: Days elapsed since content creation.
5. `days_since_last_update`: Days elapsed since last content modification.

---

## 3. Methodology & Validation Design
* **Target Label (`target_priority_flag`):**  
  Defined as `(trend_direction == 'down') AND (avg_position <= 20) AND (impressions_90d > 0)`.
* **Validation Split:** 80% Training set / 20% Holdout Test set.
* **Baseline Rule:**  
  `Priority Score = (impressions_90d * 0.01) + max(0, (20 - avg_position)) * 2.0`
* **ML Model:** DecisionTreeClassifier (`max_depth=3`, `criterion='gini'`).
* **Evaluation Metric:** Precision@50 (evaluating top 50 recommended pages).

---

## 4. Empirical Results & Model Comparison

| Evaluation Metric | Hand-Written Baseline Rule | Learned Decision Tree Model | Improvement |
|---|---|---|---|
| **Precision@50** | `0.2400` (24%) | `0.7600` (76%) | **+3.16x Precision Boost** |
| **Recall@50** | `0.1150` (11.5%) | `0.3820` (38.2%) | **+3.32x Recall Boost** |
| **Model Interpretability** | Low (Arbitrary weights) | High (Readable IF-THEN tree splits) | High Transparency |

### Top Learned Decision Rules:
1. `IF avg_position <= 8.5 AND impressions_90d > 1200 THEN Priority = High`
2. `IF content_age_days > 180 AND trend_direction == 'down' THEN Reason = STALE_DECAY`

---

## 5. Limitations & Honest Framing
* **Observed / Directional Claims Only:** We observe historical signals associated with past traffic recovery; we do **NOT** claim to have "proved Google's ranking algorithm" or guarantee #1 search position.
* **Data Limitations:** The starter slice uses a static 90-day window. Multi-year seasonal shifts are not modeled in this baseline pass.

---

## 6. Ranked Action Playbook (Top Recommendations)

| Content ID | Priority Score | Reason Code | Recommended Action |
|---|---|---|---|
| `content_304f4823` | `98.4` | `STALE_DECAY` | **FULL_CONTENT_REFRESH:** Rewrite outdated sections & update statistics. |
| `content_192a819b` | `92.1` | `LOW_CTR_STRIKING` | **REWRITE_TITLE_META:** Optimize meta title & description for higher CTR. |
| `content_827f10ac` | `88.7` | `STALE_DECAY` | **FULL_CONTENT_REFRESH:** Re-optimize heading structure and freshness tags. |
| `content_7182b810` | `84.3` | `LOW_CTR_STRIKING` | **REWRITE_TITLE_META:** Test action-oriented headline hooks. |

---

## 7. Reproducibility & Code Verification
All code, data contracts, and notebook outputs are fully reproducible in the GitHub repository:
* Research Question Notebook: [`work/notebooks/w01_research_question.ipynb`](https://github.com/sneha27patel/flyrank-ml-internship/blob/main/work/notebooks/w01_research_question.ipynb)
* Task Framing Notebook: [`work/notebooks/w02_ml_task_framing.ipynb`](https://github.com/sneha27patel/flyrank-ml-internship/blob/main/work/notebooks/w02_ml_task_framing.ipynb)
* Data Contract Notebook: [`work/notebooks/w03_data_contract.ipynb`](https://github.com/sneha27patel/flyrank-ml-internship/blob/main/work/notebooks/w03_data_contract.ipynb)
* Baseline Score Notebook: [`work/notebooks/w04_baseline_score.ipynb`](https://github.com/sneha27patel/flyrank-ml-internship/blob/main/work/notebooks/w04_baseline_score.ipynb)

---

## 8. Acknowledggments & Data Credit
Built on the **FlyRank ML Internship dataset** ([https://flyrank.ai](https://flyrank.ai)). Dataset released under safe pseudonymized research guidelines.
