# FL-02: Prompting Fundamentals on Real Tasks
**Intern:** Sneha Patel  
**Track:** General AI Fluency / FlyRank ML Internship  
**Target Task:** Python Code Debugging & Pipeline Refactoring for ML Pipelines  

---

## 1. Prompt Iteration Log (6 Iterations)

### Version 0: Naive Baseline (Run 0)
* **Technique:** None (Naive one-line prompt)
* **Prompt:** `"Fix this python pandas ML code error."`
* **Output Excerpt:** Generic list of standard pandas error types (KeyError, ValueError) with basic advice to check null values.
* **Observed Change Note:** Vague prompt yields generic troubleshooting advice rather than actionable fixes.

---

### Version 1: Role Assignment (Run 1)
* **Technique:** Role Assignment
* **Prompt:** `"Act as a Senior ML Infrastructure Engineer specializing in Python and pandas pipelines. Fix this code error."`
* **Output Excerpt:** Adopts a technical engineering tone and focuses on vectorization, memory optimization, and structured debugging.
* **Observed Change Note:** Tone shifted from beginner tutorial to professional engineering advisor.

---

### Version 2: Context & Motivation (Run 2)
* **Technique:** Context and Motivation
* **Prompt:** `"Act as a Senior ML Engineer. I am working on a FlyRank search intelligence pipeline in Google Colab. My pandas script fails with FileNotFoundError when loading 'data/raw/content_refresh_anonymized.csv'. Fix this error."`
* **Output Excerpt:** Correctly identifies that Google Colab runs in an isolated container where local relative paths don't exist by default; suggests URL fallback.
* **Observed Change Note:** Providing environment context (Colab) allowed the AI to diagnose the exact root cause.

---

### Version 3: Output Structuring (Run 3)
* **Technique:** Output Structuring
* **Prompt:** `"Act as a Senior ML Engineer. Fix the Colab FileNotFoundError when reading content_refresh_anonymized.csv. Format your response into 3 sections: 1. Root Cause (1 sentence), 2. Corrected Code Block (Python only), 3. Verification Check."`
* **Output Excerpt:** Clean 3-part structured response with isolated executable Python code and no markdown fluff.
* **Observed Change Note:** Output formatting eliminated conversational fluff, providing instant copy-paste code.

---

### Version 4: Few-Shot Examples (Run 4)
* **Technique:** Few-Shot Examples
* **Prompt:** `"Act as a Senior ML Engineer. Fix path loading errors using a defensive fallback pattern like this example:  
Example:  
try: df = pd.read_csv('local.csv')  
except Exception: df = pd.read_csv('https://raw.github.../local.csv')  
Apply this pattern to load content_refresh_anonymized.csv."`
* **Output Excerpt:** Generates exact defensive `try-except` pattern catching both local path errors and HTTP download fallbacks.
* **Observed Change Note:** Providing a concrete example forced the AI to follow defensive coding guidelines instead of introducing complex external libraries.

---

### Version 5: Step Decomposition (Run 5)
* **Technique:** Step Decomposition
* **Prompt:** `"Act as a Senior ML Engineer. Refactor the dataset loader in 3 explicit steps: Step 1: Try local path; Step 2: Fall back to raw GitHub URL if local fails; Step 3: Print dataframe shape and verify column 'content_id' exists. Output only executable Python code."`
* **Output Excerpt:** Step-by-step verified Python script performing path check, URL fallback, and schema validation.
* **Observed Change Note:** Decomposing steps ensured schema validation was included alongside error handling.

---

## 2. Cross-Model Comparison: Claude vs. ChatGPT

| Dimension | Claude (Claude 3.5 Sonnet) | ChatGPT (GPT-4o) |
|---|---|---|
| **Tone & Style** | Direct, concise, highly modular, no unnecessary conversational text. | Friendly, detailed, includes introductory and explanatory commentary. |
| **Code Precision** | Includes explicit schema validation (`df.columns`) and defensive fallbacks by default. | Focuses on functional correctness, but required explicit prompts to omit conversational text. |
| **Failure Points** | Can assume variables exist if not explicitly constrained in prompt. | Tends to offer alternative libraries (`google.colab.drive`) when a simple fallback URL is cleaner. |

---

## 3. Final Reusable Prompt Template

```text
[Role]: Act as a Senior ML Engineer specializing in Python data pipelines.
[Context]: I am processing tabular search data in a notebook environment where local file paths may be missing.
[Task]: Write a defensive dataset loader in Python for a CSV file.
[Constraints]: 
1. Use a try-except fallback: attempt reading local path '{LOCAL_FILE_PATH}', and if it fails, read directly from raw URL '{GITHUB_RAW_URL}'.
2. Verify that required columns {REQUIRED_COLUMNS} exist in the loaded dataframe.
3. Print dataframe shape upon successful loading.
[Output Format]: Return ONLY executable Python code with short inline comments. No markdown explanation outside the code block.
