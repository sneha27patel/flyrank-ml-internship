# FL-04: Automation Workflow Walkthrough
**Author:** Sneha Patel  
**Track:** General AI Fluency / FlyRank ML Internship  
**Pipeline Selected:** ML Research & Search Intelligence Synthesis Pipeline  

---

## 1. Step Diagram (Workflow Flow)

- **Step 1: Raw Ingestion** (NotebookLM Source)
- **Step 2: Signal Extraction** (Claude Structured Prompt)
- **Step 3: Executive Summary** (Claude Project Template)
- **Step 4: Human Review** (Sanity Check & Audit)

---

## 2. Prompts & Configuration Used

* **Step 1 Configuration (NotebookLM):** Ingest raw dataset documentation and CSV manifests to establish grounded truth.
* **Step 2 Prompt (Signal Extraction):**  
  > *"Act as a Data Analyst. Extract 5 core search performance metrics (impressions, average position, CTR, content age, trend) from the input text and format as a clean JSON object."*
* **Step 3 Prompt (Executive Summary Drafting):**  
  > *"Act as an Applied ML Lead. Take the extracted JSON metrics and draft a 3-paragraph executive summary detailing content refresh priorities and expected traffic recovery."*
* **Step 4 (Human Audit Rule):** Compare output numbers against original raw dataset to ensure zero metric hallucination.

---

## 3. Five Real Runs & Outputs

1. **Run 1 (Content Refresh Dataset Slice):** Ingested `content_refresh_anonymized.csv`. Outputted 1-page action queue for declining pages.
2. **Run 2 (Client Dimension Audit):** Ingested `dim_clients.csv`. Verified `gsc_data_start` dates across 104 client records.
3. **Run 3 (Daily Traffic Performance Fact Table):** Ingested daily fact table. Identified top 10 pages in continuous 30-day decline.
4. **Run 4 (Hugging Face Warehouse Logs):** Ingested Parquet DuckDB query outputs. Summarized row counts and date spans.
5. **Run 5 (ML Model Report Output):** Ingested DecisionTree model metrics. Formatted Precision@50 comparison table vs. baseline rules.

---

## 4. Time-Saved Accounting (Honest Time Breakdown)

| Activity | Manual Time | Automated Workflow Time |
|---|---|---|
| Initial Workflow Setup Cost | 0 min | 30 min (One-time setup) |
| Running 5 Summaries | 225 min (45 min/run) | 20 min (4 min/run) |
| Human Sanity Check / Verification | 0 min | 25 min (5 min/run) |
| **Total Time Spent** | **225 minutes** | **75 minutes total** |

* **Net Time Saved:** **150 minutes saved across 5 runs (66% time reduction)**.

---

## 5. Known Failure Points & Required Human Review

* **Failure Point 1 (Unbalanced Panel Lag):** AI can mistake "missing GA4 tracking" (`ga4_data_available == FALSE`) for "zero traffic."
  * *Human Review Action:* Manually verify client tracking start dates before flagging zero-session pages.
* **Failure Point 2 (Correlation vs. Causality):** AI summaries tend to overclaim that refreshing a page guarantees a #1 position.
  * *Human Review Action:* Edit summary text to use cautious decision-support language ("observed directional signal").
