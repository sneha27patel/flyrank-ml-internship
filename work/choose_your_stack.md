# Three Roads: Stack Trade-off Analysis & Rationale
**Author:** Sneha Patel  
**Track:** General AI Fluency / FlyRank ML Internship  

---

## 1. Constraints & Portfolio Requirements
* **Cost Constraint:** 100% Free hosting and tooling.
* **Skill Level:** Intermediate Python & basic HTML/CSS/JS.
* **Work Display Needs:** Code repositories, markdown notebooks, data tables, and static charts.
* **Backend Requirement:** "Not yet" (Static client-side presentation is sufficient; backend APIs are unnecessary for this portfolio phase).

---

## 2. The Three Stack Options Considered

### Option 1 (Simplest): Static HTML5 / Vanilla CSS on GitHub Pages
* **Build Method:** Hand-crafted HTML5 & CSS3 with Inter typography.
* **Hosting:** Free GitHub Pages directly from main branch.
* **Backend Needed:** No.
* **Trade-off:** Fast, zero build configuration, 100% reliable, but manual repetition across multiple HTML pages.

### Option 2 (Moderate): Vite + React on Vercel / GitHub Pages
* **Build Method:** Vite dev server with React component abstraction.
* **Hosting:** Vercel or GitHub Pages via GitHub Actions workflow.
* **Backend Needed:** No.
* **Trade-off:** Reusable UI components, but requires managing `npm` dependencies, node modules, and build pipelines.

### Option 3 (Most Powerful): Next.js App Router + TailwindCSS on Vercel
* **Build Method:** Full-stack React framework with SSR and file-based routing.
* **Hosting:** Vercel Hobby Tier.
* **Backend Needed:** Optional API routes.
* **Trade-off:** High feature capability, but significant framework complexity, potential deployment breaking changes, and unnecessary overhead for a static portfolio.

---

## 3. Pressure-Testing & Decision Rationale

### **Chosen Stack:** Option 1 — Static HTML5 / Vanilla CSS on GitHub Pages

### **Why I Chose Option 1 (The Simplest Road):**
1. **Can I maintain this?** Yes! Plain HTML/CSS requires zero node module updates, build tools, or framework migrations. It will run reliably for years without maintenance debt.
2. **Does it show my work well?** Absolutely. My core deliverables are Jupyter notebooks, scikit-learn metrics, and research write-ups. HTML/CSS renders code snippets and tables cleanly without framework overhead.
3. **Can I finish in two weeks?** Yes. Avoiding React/Next.js setup allows me to spend 100% of my time refining ML data models and case study write-ups rather than debugging web build pipelines.

### **Why I Rejected Option 2 & Option 3:**
* **Option 2 (Vite+React):** Rejected because managing component props and build bundlers adds unnecessary friction when static HTML accomplishes the exact same visual outcome.
* **Option 3 (Next.js):** Rejected because a dynamic backend framework is overkill when all portfolio data is static. Choosing "not yet" for backend keeps the build lean and unblocked.
