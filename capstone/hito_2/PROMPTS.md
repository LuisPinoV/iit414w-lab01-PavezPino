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

## Interaction 3: Choose baselines for `is_top3`

**Context:**
The initial `is_top3` comparison used only a prevalence baseline. To strengthen the case for the model, we added a domain-motivated baseline: a simple rule based on grid position.

**Prompt:**
> "Design two baselines for the is_top3 model: one that represents 'no signal' and one that represents a simple domain rule. The rule should be operationally realistic in F1 and use only pre-race features."

**Output:**
AI-assisted baseline design produced:
1. **Prevalence baseline**: Predict constant `P(is_top3)` = 0.154 (train set mean)
2. **Grid-position rule baseline**: Assign `P(is_top3) = 0.35` if grid_position ≤ 5, else `0.10`

The grid-position rule reflects the well-known F1 fact that drivers starting in the front rows have much higher podium probability.

**Validation:**
We computed Brier scores for both baselines on the test set:
- Prevalence Brier = 0.1311 (no discrimination, ROC-AUC = 0.5)
- Grid-position rule Brier = 0.0964 (reasonable discrimination, ROC-AUC = 0.8556)
- Our model Brier = 0.0789 (best, ROC-AUC = 0.9212)

**Final decision:**
We kept both baselines in the comparison to show that: (a) the model beats a no-signal baseline by 39.8%, and (b) the model improves over a reasonable domain rule by 18.2%.

---

## Interaction 4: Draft the analysis tables

**Context:**
We needed the baseline comparison, slice analysis, leakage note, and mitigation plan written in a way that matches the code outputs and does not overclaim causality.

**Prompt:**
> "Turn the model outputs and baselines into concise markdown artifacts for baseline comparison, error analysis, leakage audit, mitigations, and the README / runbook. Keep the language scenario-conditioned and consistent with the notebook."

**Output:**
AI-assisted drafting produced concise markdown with the correct metrics, slice summaries, baseline comparisons, and confounding caveat.

**Validation:**
We cross-checked the text against the notebook outputs and the held-out metrics. The final values used in the markdown files match the notebook calculations.

**Final decision:**
The markdown files in this folder are the report-ready version of the Hito 2 analyses.

---

## Interaction 5: Implement stacking to leverage is_top10 for is_top3

**Context:**
The user requested that the `is_top3` model use the calibrated predictions from the `is_top10` model as an additional feature. This stacking approach exploits the logical dependency (top 3 requires top 10) and allows `is_top3` to leverage the signal learned by the primary model.

**Prompt:**
> "Use the is_top10 model output as a training variable for is_top3. Generate calibrated predictions on train/calib/test separately to avoid leakage, then add P(is_top10) as a meta-feature to the is_top3 feature set."

**Output:**
AI-assisted implementation:
1. Trained `is_top10` model on standard 20 features (unchanged)
2. Generated calibrated `is_top10` probabilities on each split independently
3. Created stacked datasets with `P(is_top10)` column added
4. Defined `fit_binary_stacked()` and `evaluate_binary_stacked()` functions with expanded feature set (21 features)
5. Trained `is_top3` model on stacked features
6. Updated `score_pair()` to compute `is_top3` predictions using stacked features

**Results:**
- `is_top3` Brier improved from baseline to **0.0789** (stacked)
- Outperforms prevalence baseline by **39.8%**
- Outperforms grid-position rule baseline by **18.2%**
- What-if pair still shows target disagreement (Bahrain VER, 1-stop vs 2-stop)
- Leakage guard maintained: probabilities generated per-split, scenario inputs only in what-if

**Validation:**
- All cells executed cleanly with no errors
- What-if pair scoring produces expected disagree verdict
- Error analysis tables generated correctly by slice (strategy/circuit/constructor)
- Baseline improvements verified through Brier score comparison

**Final decision:**
Stacking is activated. The `is_top3` model now leverages `is_top10` predictions as a meta-feature, improving calibration and discrimination on the expansion target.

