FL-04: Automation Workflow Walkthrough
Author: Sneha Patel
Track: General AI Fluency / FlyRank ML Internship
Pipeline Selected: ML Research & Search Intelligence Synthesis Pipeline

1. Step Diagram (Workflow Flow)
Trigger: Need to analyze content performance data from content_refresh_anonymized.csv (30,000 pages × 44 columns across 32 clients) and generate actionable content refresh recommendations.


Step 1: Raw Ingestion (NotebookLM)
    ↓  Upload CSV + data dictionary as sources
Step 2: Signal Extraction (Claude Structured Prompt)
    ↓  Extract key performance metrics into structured JSON
Step 3: Executive Summary (Claude Project Template)
    ↓  Convert extracted signals into actionable insights
Step 4: Human Review (Manual Sanity Check & Audit)
    ↓  Verify AI outputs against raw data for accuracy
Handoff Pattern:

Step 1 → Step 2: NotebookLM's grounded summary text is copy-pasted into Claude as input context
Step 2 → Step 3: JSON output from the extraction prompt becomes the input for the summary drafting prompt
Step 3 → Step 4: The AI-generated executive summary is manually compared against the original CSV data and the pipeline's model_report.md output
2. Prompts & Configuration Used
Step 1 Configuration (NotebookLM)
Sources uploaded: content_refresh_anonymized.csv (30,000 rows), data-dictionary.md (all 44 column definitions), and model_report.md (pipeline output with model comparison results)
Goal: Let NotebookLM ground itself on the real dataset so it can answer questions about the data without hallucinating column names or statistics
Configuration: Default NotebookLM settings, no custom instructions. Three source files uploaded as the knowledge base.
Step 2 Prompt (Signal Extraction — Claude)
"You are a data analyst at a search intelligence company. I will give you a text summary of a content performance dataset containing 30,000 pages across 32 clients, with 90-day trailing metrics from Google Search Console and GA4.

Extract exactly 5 key metrics from the summary:

Total number of pages analyzed
Percentage of pages with declining traffic (trend_direction = 'down')
Average search position across all pages
Median CTR (note: CTR values are ×100 percentages, so 0.76 means 0.76%, not 76%)
Most common content type and main intent combination
Format your response as a clean JSON object with keys: total_pages, declining_pct, avg_position, median_ctr, common_content_intent."

Step 3 Prompt (Executive Summary — Claude)
"You are an Applied ML Lead at FlyRank, a search intelligence company. Take the following JSON metrics extracted from a 30,000-page content portfolio and draft a 3-paragraph executive summary:

Paragraph 1: Current state of the content portfolio — total pages, client count, overall health signal
Paragraph 2: Key risk signals — what percentage is declining, position and CTR problems, content age concerns
Paragraph 3: Recommended priorities for content refresh, ordered by expected traffic recovery impact. Reference the pipeline's suggested actions: 'refresh', 'refresh_and_review_ctr', 'refresh_and_review_engagement', and 'expand_and_refresh'
Keep the tone professional but actionable. Each recommendation must tie back to a specific metric from the data."

Step 4 (Human Audit Rules)
Compare every number in the AI summary against the original CSV and model_report.md
Verify declining rate matches: pipeline reports 16,262 / 30,000 = 54.2%
Check that CTR interpretation is correct (×100 percentages, not raw percentages)
Verify no hallucinated client names or domain names (data is pseudonymized)
Cross-check suggested action distribution against the pipeline's final queue output
3. Five Real Runs & Outputs
Run 1: Content Portfolio Overview via NotebookLM
Input: Uploaded content_refresh_anonymized.csv (30,000 rows × 44 columns) and data-dictionary.md into NotebookLM
What I did: Asked NotebookLM "What are the key patterns in this dataset? How many pages are declining?"
Output: NotebookLM identified that the dataset contains 30,000 content items across 32 pseudonymized clients. It noted that all metrics are aggregated over a trailing 90-day window. It highlighted that trend_direction is the key label column, with pages classified as "down", "up", or "stable". It correctly warned that rate columns (ctr, engagement_rate, scroll_rate) are ×100 percentages — so ctr = 0.76 means 0.76%, not 76%.
Insight: NotebookLM was able to ground its answers in the actual data dictionary, which prevented the common mistake of misinterpreting CTR values. This grounding step is essential before feeding data summaries to Claude.
Run 2: Signal Extraction from Summary
Input: Pasted NotebookLM's grounded summary text into Claude along with the Step 2 extraction prompt
Output JSON:
json

{
  "total_pages": 30000,
  "declining_pct": 54.2,
  "avg_position": 18.7,
  "median_ctr": 0.12,
  "common_content_intent": "keyword article / informational"
}
Insight: Claude correctly extracted the declining rate (54.2% matches the pipeline's 16,262/30,000). The most common content type being "keyword article" with "informational" intent matches the dataset composition. The median CTR of 0.12% (remember, ×100 scale) indicates very low click-through rates across the portfolio, which aligns with the pipeline flagging many pages for CTR review.
Run 3: Executive Summary Generation
Input: Fed the Run 2 JSON into Claude along with the Step 3 executive summary prompt
Output:
Portfolio Health Overview: The content portfolio spans 30,000 pages across 32 client accounts, representing a substantial body of search-indexed content. With an average search position of 18.7 and a median CTR of just 0.12%, the portfolio shows meaningful search visibility but significant underperformance in converting impressions to clicks.

Critical Risk Signals: Over half the portfolio (54.2%) shows declining traffic trends, signaling widespread content decay. Pages ranking on positions 11–20+ are accumulating impressions without clicks, creating a large pool of "striking distance" opportunities. The combination of high decline rates and low CTR suggests that many pages were once performing but have lost freshness relevance or fallen behind competitors' updated content.

Refresh Priorities: Immediate action should focus on three tiers: (1) "refresh_and_review_ctr" pages — these have search visibility but critically low click-through, often fixable by updating meta titles and descriptions; (2) "refresh" pages — declining content that needs full content updates to recover traffic; (3) "expand_and_refresh" pages — a smaller set (82 items per the pipeline queue) where thin content needs both expansion and modernization. The CTR review candidates should be prioritized first as they represent the fastest path to traffic recovery with minimal content production effort.

Insight: The summary is well-structured and actionable. Claude correctly identified "striking distance" pages as a key opportunity. However, it slightly generalized the position data — saying "positions 11–20+" when the actual data shows a wider spread. This needed correction in the audit step.
Run 4: Human Audit & Verification
Input: Compared Run 3's executive summary against the pipeline's model_report.md and refresh_queue_sample.csv
Findings:
✅ Declining rate of 54.2% matches exactly (model_report: 16,262 / 30,000 = 0.542)
✅ Action category counts are correct: pipeline shows 8,178 "refresh", 6,657 "refresh_and_review_ctr", 1,990 "refresh_and_review_engagement", 82 "expand_and_refresh"
⚠️ The "average position 18.7" needs verification — this was from Claude's extraction, not directly from the raw CSV. Spot-checking the top 30 queue entries shows positions ranging from 1.9 to 29.9, so 18.7 is plausible but should be confirmed with a full CSV calculation
✅ No hallucinated client names or domains — Claude correctly used pseudonymized IDs
⚠️ Claude said "positions 11–20+" which oversimplifies the position_tier distribution (top_3, page_1, striking, page_3_5 are the actual tiers in the data)
Insight: The pipeline end-to-end was ~90% accurate. The main risks are: (a) averaged statistics can mask important distributions, and (b) Claude tends to generalize specific data tiers into broader ranges. Human audit caught both issues.
Run 5: Refined Prompt — Better Position Analysis
What changed: Based on Run 4 audit findings, I modified the Step 3 prompt to include the actual position_tier categories from the data: top_3, page_1, striking, page_3_5
Updated prompt addition: Added: "When discussing position data, use the exact position tiers from the dataset: top_3 (positions 1-3), page_1 (positions 4-10), striking (positions 11-20), page_3_5 (positions 21-50). Do not generalize into custom ranges."
New output (Paragraph 2, revised):
Critical Risk Signals: Over half the portfolio (54.2%) shows declining traffic. The position distribution reveals that many declining pages sit in the "striking" tier (positions 11-20) and "page_1" tier (positions 4-10) — these are pages with real visibility that are losing ground. Pages in "page_3_5" (positions 21-50) represent longer-term recovery projects, while "top_3" pages need protective monitoring rather than aggressive refresh.

Insight: Adding explicit tier definitions to the prompt eliminated Claude's tendency to invent its own position ranges. The revised output directly uses the dataset's vocabulary, making it more accurate and immediately useful for someone reviewing the actual data. Lesson: always feed domain-specific vocabulary into the prompt rather than expecting the model to infer it.
4. Reflection
What worked well:

NotebookLM as the ingestion layer was excellent — it grounded all subsequent steps in the actual data, preventing hallucination of column names and statistics
The 4-step handoff pattern (ingest → extract → summarize → audit) created clear checkpoints where errors could be caught
Using the pipeline's own model_report.md as the audit reference made verification fast and objective
What I would change next time:

Add a Step 2.5 where I run a quick Python script to compute exact summary statistics (mean, median, percentiles) from the CSV before asking Claude to interpret them — this would eliminate the "plausible but unverified" numbers issue from Run 4
Include more specific data vocabulary in the initial prompts instead of discovering gaps during audit
Run the extraction prompt on smaller data slices (per client_id) to catch client-specific patterns that get lost in portfolio-wide averages
Key takeaway: Building an AI workflow is not about getting a perfect output in one shot — it's about creating a repeatable system with clear handoffs and audit checkpoints so that each run gets more accurate than the last.
