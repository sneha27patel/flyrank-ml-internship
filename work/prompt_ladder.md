# The Prompt Ladder Assignment
**Intern:** Sneha Patel  
**Track:** General AI Fluency / FlyRank ML Internship  

---

## 1. Baseline Prompt (Run 0 - Weak Baseline)

* **Prompt:** `"Help me analyze search data for my ML model."`
* **Output Excerpt:** Generic AI textbook list suggesting standard Python libraries (`pandas`, `matplotlib`, `scikit-learn`) and basic advice like checking for null values.
* **Output Notes:** Extremely vague, generic, and lacks any search domain context.

---

## 2. Layer 1: Persona & Domain Context (Run 1)

* **Layer Added:** Role definition + domain background.
* **Prompt:** `"Act as a Senior ML Engineer at FlyRank. I am analyzing search traffic data containing impression counts, click-through rates (CTR), page age, and position tiers to identify decaying content."`
* **Output Excerpt:** Focuses on search intelligence concepts, suggesting metrics like impression drop-off and position volatility.
* **Output Notes:** Much better domain focus, but output is still unstructured and too conversational.

---

## 3. Layer 2: Task Definition & Format Constraints (Run 2)

* **Layer Added:** Specific task requirements + format constraints.
* **Prompt:** `"Act as a Senior ML Engineer at FlyRank. I am analyzing search traffic data (impressions, CTR, position, age). Write Python pandas code to filter pages that rank in the top 20 average position but have a 'down' trend_direction and impressions > 0."`
* **Output Excerpt:** Executable pandas filtering snippet creating a boolean mask.
* **Output Notes:** Clear code output, but the code lacks exception handling for missing local CSV files.

---

## 4. Layer 3: Edge Case Handling & Fallbacks (Run 3)

* **Layer Added:** Defensive coding rules + GitHub URL fallback.
* **Prompt:** `"Act as a Senior ML Engineer at FlyRank. Write pandas code to filter decaying search pages (trend_direction=='down', avg_position<=20, impressions_90d>0). Constraint: Do not assume local CSV files exist—fetch directly from GitHub raw URL with a try-except fallback."`
* **Output Excerpt:** Robust Python script reading from raw GitHub URL inside a `try-except` block.
* **Output Notes:** Code is completely runnable without `FileNotFoundError`, but it just filters rows without ranking priority.

---

## 5. Layer 4: Over-Engineering Test (Run 4 - "This Made It Worse" Moment)

* **Layer Added:** Complex custom Bayesian mathematical composite score.
* **Prompt:** `"Act as a Senior ML Engineer at FlyRank. Write code to score decaying search pages using a custom 5-variable weighted Bayesian composite formula combining logarithmic position weights and normalized age tiers."`
* **Output Excerpt:** A complex mathematical formula that outputs opaque composite scores.
* **Output Notes (Honest Reflection - Made It Worse):**  
  *This iteration made the output worse!* The complex formula created a black-box metric that content editors cannot understand or trust. A simple interpretable Decision Tree model is much better than a tangled custom formula.

---

## 6. Layer 5: Final Reusable Prompt (Run 5 - Clean Reusable Baseline)

* **Layer Added:** Simplification to interpretable Decision Tree + Precision@50 evaluation.
* **Final Reusable Prompt:**
  > `"Act as an Applied ML Lead. Given an anonymized search traffic dataframe `df` with columns `['content_id', 'trend_direction', 'avg_position', 'ctr', 'impressions_90d']`:  
  > 1. Define a binary target column `target_priority` where `trend_direction == 'down'` and `avg_position <= 20`.  
  > 2. Train an interpretable DecisionTreeClassifier (max_depth=3).  
  > 3. Evaluate using Precision@50.  
  > 4. Output the top decision rules and clean executable Python code without markdown fluff."`

* **Output Excerpt:** Clean 15-line Python script training a depth-3 tree, printing readable `if/else` rules and exact Precision@50 score.
* **Output Notes:** Perfect balance of clarity, interpretability, and actionable ML evaluation. Works for any intern or developer on the track.
