# FL-05: Agent Concepts and MCP Basics
Author: Sneha Patel
Track: General AI Fluency / FlyRank ML Internship

---

## 1. Workflow vs Agent - The Key Difference

A WORKFLOW is a fixed pipeline where each step runs in a pre-set order decided by the developer. The AI model fills in content at each step, but it does not choose what step comes next. Example: Step 1 ingest data, Step 2 extract signals, Step 3 write summary. Always the same order.

An AGENT is a system where the AI model itself decides what to do next. It has a goal, a set of tools, and an observation loop. It picks a tool, runs it, checks the result, and decides the next action dynamically until the goal is met.

My FL-04 pipeline is a WORKFLOW. It follows a fixed 4-step sequence (ingest, extract, summarize, review). The AI does not decide which step to run next. I do.

---

## 2. What is MCP (Model Context Protocol)?

MCP is an open standard that connects AI models to external tools and data sources. Think of it as the USB-C port for AI applications. It has three primitives:

- TOOLS: Executable functions the model can call (e.g., read a file, run a query, list a directory)
- RESOURCES: Read-only data the model can inspect (e.g., CSV files, database schemas)
- PROMPTS: Pre-built prompt templates provided by the MCP server

---

## 3. Three Tasks That Chat Alone Cannot Do (MCP Tool Evidence)

Task 1 - Reading Local Files:
Tool used: list_dir and view_file
What happened: The AI assistant directly inspected my local computer folders and read file contents to check repository structure. Plain chat cannot access a local filesystem.

Task 2 - Fetching Live Web Content:
Tool used: read_url_content
What happened: The assistant fetched the raw CSV header columns directly from a GitHub URL to verify exact column names (content_id vs content_hash_id). This prevented a KeyError in my Colab notebook. Plain chat cannot make live HTTP requests.

Task 3 - Running Terminal Commands:
Tool used: run_command
What happened: The assistant attempted to run git clone directly on my machine terminal. Plain chat cannot execute system commands.

---

## 4. How to Upgrade FL-04 Workflow into an Agent

To make my FL-04 pipeline an agent, I would need to add:

1. A GOAL: Achieve Precision at 50 greater than 0.70 on the content refresh scoring model.
2. TOOLS via MCP: query_duckdb (to load data), train_sklearn_model (to fit a tree), evaluate_precision (to check score).
3. A DECISION LOOP: After training, the agent checks Precision at 50. If below 0.70, it autonomously adjusts max_depth or removes noisy features and retrains. If above 0.70, it exports the final ranked CSV.

The key difference: instead of me manually checking scores and re-running cells, the agent would loop automatically until the goal is satisfied.
