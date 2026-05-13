# PROMPTS — Hito 2

This file records how AI assistance was used and how the output was validated for the Hito 2 model expansion.

---

## Interaction 1: Confirm the expansion target

**Context:**
The Hito 2 brief requires a second target, and the assignment now explicitly mandates `is_top3`. We needed to confirm the dataset supports that target and that it is worth modeling alongside `is_top10`.

**Prompt:**
> "Inspect the race-level dataset and confirm whether `is_top3` exists, what the class balance looks like, and whether it is a sensible expansion target for the midpoint model."

**Output:**
AI-assisted inspection showed that the dataset includes `is_top3` and that it is substantially less common than `is_top10`. That makes it a useful expansion target because it exposes podium-risk trade-offs that top-10 alone can hide.

**Validation:**
We checked the dataset directly and confirmed the column list includes `is_top3`. We also verified the class counts in the training split and the held-out split to make sure the target is not degenerate.

**Final decision:**
We locked Hito 2 to `is_top3` as the expansion target.

---

## Interaction 2: Find a scenario pair that actually disagrees

**Context:**
The assignment requires at least one matched what-if pair where the two targets recommend different strategies. We needed a concrete driver-race context and two strategy plans that produce that disagreement.

**Prompt:**
> "Search for a fixed test-set context and two strategy scenarios where calibrated `is_top10` and `is_top3` disagree on the preferred recommendation. Use the locked split and keep the driver-race context fixed."

**Output:**
The search found a 2023 Bahrain Grand Prix row for `VER` where the comparison between a 1-stop `S-M` strategy and a 2-stop `S-M-S` strategy produces a target disagreement. `is_top10` prefers the two-stop plan, while `is_top3` prefers the one-stop plan.

**Validation:**
We reproduced the result with provisional logistic-regression models on the locked split and confirmed the probability values:
- `P(is_top10)`: 0.9547 vs 0.9677
- `P(is_top3)`: 0.9059 vs 0.8354

**Final decision:**
We adopted that pair as the default what-if comparison in the notebook and the report.

---

## Interaction 3: Draft the analysis tables

**Context:**
We needed the baseline comparison, slice analysis, leakage note, and mitigation plan written in a way that matches the code outputs and does not overclaim causality.

**Prompt:**
> "Turn the model outputs into concise markdown artifacts for baseline comparison, error analysis, leakage audit, mitigations, and the README / runbook. Keep the language scenario-conditioned and consistent with the notebook."

**Output:**
AI-assisted drafting produced concise markdown with the correct metrics, slice summaries, and confounding caveat.

**Validation:**
We cross-checked the text against the notebook outputs and the held-out metrics. The final values used in the markdown files match the notebook calculations.

**Final decision:**
The markdown files in this folder are the report-ready version of the Hito 2 analyses.
