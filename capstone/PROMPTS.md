# AI Interactions Log — Hito 1

This document records the structured use of AI assistance in framing the Hito 1 decision and implementing the baseline.

---

## Interaction 1: Baseline design and leakage audit

**Context:**
We needed to design a defensible pre-race baseline that avoids leaking post-race strategy information into the model. The dataset contains columns like `n_stops`, `compound_sequence`, and `stint_lengths` that are observed post-race but allowed as scenario inputs. We needed clarity on which features to use in the baseline and how to separate them from scenario evaluation.

**Prompt:**

> "I have an F1 race dataset with strategy features (n_stops, compound_sequence, stint_lengths) that are post-race observations. I want to build a baseline model that predicts is_top10 using only pre-race signals (grid_position, constructor_tier, wet_day) for temporal split: train 2019–2021, calibration 2022, test 2023–2024. How do I structure a logistic regression pipeline to avoid leakage? And how should I audit the columns to ensure no post-race information leaks into the baseline?"

**Output:**
The AI suggested:

1. Use a preprocessing pipeline with `ColumnTransformer` to separate numeric and categorical features.
2. Fit on train (2019–2021), calibrate on 2022 using `CalibratedClassifierCV` with `cv='prefit'` and isotonic scaling.
3. Document which columns are pre-race (baseline), post-race strategy (scenario inputs), and audit columns (incidents).
4. Create an explicit audit dict listing columns by category to show no contamination.

**Validation:**

- We checked that the suggested approach matches the rubric requirement: "leakage audit complete."
- We verified that the baseline uses only pre-race features.
- We confirmed that the calibration block (2022) is used only for calibration mapping, not model selection.

**Adaptations:**

- We added fallback column detection using a `pick()` helper because the actual dataset column names might differ from our assumptions.
- We expanded the leakage audit to include a JSON dump for transparency.
- We clarified that scenario cells (not shown in this cell) would handle post-race features separately.

**Final Decision:**
We implemented the baseline using logistic regression with calibration on 2022, and we added an explicit leakage audit cell that categorizes all dataset columns. This satisfies the rubric requirement and makes the distinction between pre-race predictors and scenario inputs clear.

---

## Interaction 2: What-if scenario evaluation and bootstrap confidence intervals

**Context:**
Once the baseline was built, we needed to evaluate what-if scenarios (Scenario A: 1-stop, Scenario B: 2-stop, Scenario C: context shift with wet weather). The rubric requires concrete feature values and bootstrap confidence intervals for the comparison. We wanted to understand how to compute calibrated probabilities on scenario rows and report uncertainty.

**Prompt:**

> "I have a calibrated logistic regression model and three scenario rows (A, B, C) defined in my framing.md. Scenario A has grid_position=12, n_stops=1; Scenario B has grid_position=12, n_stops=2; Scenario C has grid_position=12, n_stops=2 but wet_day=1. How do I compute calibrated P(is_top10) for each scenario and report the delta between A and B with a 90% bootstrap confidence interval?"

**Output:**
The AI provided:

1. Use `calibrator.predict_proba(scenario_row)[:,1]` to get calibrated probabilities for each scenario.
2. To compute bootstrap CIs, resample test-set predictions and recompute calibration on each bootstrap sample.
3. Report delta P(A) - P(B) with percentile-based confidence intervals (e.g., 5th to 95th percentile).
4. Example code structure for bootstrapping with 1000 resamples.

**Validation:**

- We checked that the approach is statistically sound for confidence interval estimation.
- We verified that resampling occurs post-calibration (not contaminating train/calib blocks).
- We confirmed that scenario rows use only the baseline features (grid_position, constructor_avg_finish_pos_5race_rolling, wet_day).

**Adaptations:**

- We simplified the initial scope: the notebook cell includes a demo of computing scenario probabilities, and we noted that the full bootstrap CI implementation would be in the extended analysis notebook for Hito 2.
- We made sure scenario rows are constructed using only baseline features to avoid accidental post-race leakage.
- We added a helper function `make_scenario()` to generate scenario DataFrames programmatically.

**Final Decision:**
We implemented a scenario evaluation cell that computes calibrated P(is_top10) for all three scenarios and reports deltas. We noted that full bootstrap CI computation will be expanded in Hito 2. This satisfies the rubric requirement for "what-if scenarios specified with concrete feature values."

---

## Interaction 3: Framing improvement based on pair-review feedback

**Context:**
After the Monday pair review, the reviewing team (TheUltrakills) gave feedback that our framing lacked depth in scenario specification and did not adequately address confounding. We needed to revise the framing to:

1. Clarify the decision objective.
2. Specify scenarios with full covariate context (grid, constructor_tier, wet_day, pit_stop_duration).
3. Acknowledge confounding and strategy-outcome dependencies.
4. Add a fallback policy if model performance doesn't improve over the docent baseline.

**Prompt:**

> "Our framing.md was reviewed and feedback indicated three issues: (1) scenarios lack depth and covariate specification, (2) we don't acknowledge strategy-outcome confounding, (3) there's no fallback policy if Brier doesn't improve. The decision is about recommending pit-stop strategy to maximize P(top-10) for midfield drivers. How should I revise the framing to address these issues while keeping it concise?"

**Output:**
The AI suggested:

1. Lock the decision objective explicitly: "Whether to recommend a one-stop or two-stop pit strategy that maximizes probability of scoring points (top-10)..."
2. Upgrade Section 4 (what-if) to include controlled scenario pairs (A vs B with fixed context) and a context-shift pair (C) to check sensitivity.
3. Add explicit confounding limitation in Section 5.
4. Add a fallback hypothesis in Section 6: "If model gains are marginal, calibrated scenario ranking with uncertainty bounds will still provide a defensible recommendation policy."

**Validation:**

- We verified that the new decision objective matches the rubric requirement (decision context, decision-maker, time window, target, metric).
- We checked that the three scenarios have concrete feature values: grid_position, constructor_tier, wet_day, pit_stop_duration, n_stops, compound_sequence, stint_lengths.
- We confirmed that confounding limitation is now acknowledged with consequence.
- We reviewed the fallback policy against the rubric criterion ("honest reflection").

**Adaptations:**

- We changed the baseline from "including n_stops" to "pre-race features only" to avoid post-race leakage.
- We elevated two of the five dataset limitations to Section 5 with explicit consequences.
- We rewrote Section 6 experiments to include scenario testing with bootstrap intervals.

**Final Decision:**
We updated framing.md across Sections 1, 3, 4, 5, 6 with the improvements. This strengthens our team's alignment with the rubric and addresses the pair-review critique. The updated framing is now submitted as the official decision sheet.

### Interaction 4: Transalte of final Report

**Context:**

We needed to translate and refine the grammar of the technical documentation while preserving the original meaning, structure, and formatting of the LaTeX source. The goal was to improve readability and consistency without modifying the technical content or introducing formatting issues in the compiled document.

**Prompt:**

> "Translate and improve the grammar of this LaTeX document while preserving all commands, equations, references, citations, and formatting exactly as written. Do not alter the structure or technical meaning of the content."

**Output:**

The AI suggested:

1. Using Claude Code and Gemini to perform translation and grammar correction directly on the LaTeX source.
2. Preserving all LaTeX commands, environments, equations, citations, labels, and references unchanged.
3. Applying only linguistic improvements to the plain-text portions of the document.
4. Keeping the original structure and technical meaning intact throughout the process.

**Validation:**

* We verified that all LaTeX syntax and document structure were preserved after processing.
* We checked that equations, references, and citations compiled correctly without modifications.
* We confirmed that the translated text maintained the same technical meaning as the original document.

**Adaptations:**

* We integrated Claude Code and Gemini into the LaTeX workflow to process plain text directly, reducing unnecessary uploads and processing costs.
* We added manual review steps to ensure consistency in terminology across sections.
* We preserved all original formatting and document organization during translation and grammar correction.

**Final Decision:**

We used Claude Code and Gemini for translation and grammar correction of the LaTeX document while maintaining the complete original structure, formatting, and technical content unchanged. This reduced processing overhead by working directly with plain text and ensured consistency across the final document.

<audio class="fixed start-0 bottom-0 hidden h-0 w-0" autoplay="" crossorigin="anonymous"></audio>


---

## Summary

Three AI interactions helped us:

1. Design a leakage-resistant baseline and audit structure.
2. Implement scenario evaluation with calibrated probabilities.
3. Revise the framing to address pair-review feedback on confounding and fallback policy.
4. Translate

All AI suggestions were validated against the rubric and adapted to fit our specific decision context. No AI output was used directly without review and manual adjustment.
