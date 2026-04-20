# PROMPTS.md - Lab 3 AI Traceability Log

## Entry 1 - 2026-04-19 - Build initial comparison notebook

**Context:** Create a full, reproducible notebook for Lab 3 with one framing, one primary metric, temporal split, and at least 2 baselines.
**Prompt(s):** "Create a regression workflow for F1 points prediction with MAE, temporal holdout, and a model comparison table including train-test gap."
**Output:** AI proposed regression with MAE, train 2022-2023 and test 2024, two baselines (global mean and grid heuristic), plus Linear Regression, Ridge, and Random Forest.
**Validation:** I executed the notebook and checked that outputs were visible and consistent. The comparison output showed all models using the same metric and split.
**Adaptations:** I kept the model set but later changed split details and Ridge tuning based on new requirements.
**Final Decision:** Partially used - the structure was useful, but some decisions were later modified.

## Entry 2 - 2026-04-19 - Fix execution and reproducibility issues

**Context:** Ensure the notebook runs end-to-end and satisfies visible-output requirements.
**Prompt(s):** "Diagnose why notebook execution is not running correctly and fix reproducibility blockers."
**Output:** AI identified notebook setup issues and suggested fixing execution environment and rerunning all cells.
**Validation:** After fixes, execution produced visible outputs including environment info and model table outputs.
**Adaptations:** I reran all relevant code cells and confirmed RANDOM_SEED = 414 behavior through repeated execution.
**Final Decision:** Used - this step was required to satisfy reproducibility and output visibility criteria.

## Entry 3 - 2026-04-20 - Remove table columns and move reasoning to markdown

**Context:** User requested removing the columns Validation and Why this result from the code table.
**Prompt(s):** "Remove the Validation and WHY columns from the computed table and move explanation to a markdown section right after the comparison cell."
**Output:** AI updated the comparison code to keep only Rank, Model, Train MAE, Test MAE, Gap; and created a markdown block with per-model reasoning.
**Validation:** I re-executed the comparison cell and verified the rendered dataframe no longer included those two columns.
**Adaptations:** I kept the interpretation text in markdown so reasoning still exists for grading C2 without polluting the metric table.
**Final Decision:** Used - this matches the requested notebook presentation.

## Entry 4 - 2026-04-20 - Expand train years and tune Ridge alpha

**Context:** New requirement: train must use 2020, 2021, 2022, 2023 and Ridge must test alphas 0.01, 0.1, 1, 10, 100, 1000.
**Prompt(s):** "Update train seasons to 2020-2023 and add temporal Ridge alpha tuning, then keep the best alpha for final test evaluation."
**Output:** AI added temporal tuning for Ridge: fit on 2020-2022, validate on 2023, choose best alpha, retrain on 2020-2023, then test on 2024.
**Validation:** Executed outputs now print:
- Train rows: 1660 | Test rows: 479
- Ridge alpha candidates: [0.01, 0.1, 1, 10, 100, 1000]
- Best Ridge alpha (val on 2023): 10 | Val MAE: 3.356
The comparison table also shows model "Ridge (best alpha=10)".
**Adaptations:** I also updated the data-loading seasons to include 2020 and 2021, because changing only TRAIN_SEASONS would not include those rows.
**Final Decision:** Used - this is the final configuration in the notebook.

## Entry 5 - 2026-04-20 - Align narrative artifacts with executed outputs

**Context:** Keep written deliverables consistent with actual notebook results and decisions.
**Prompt(s):** "Update written files so claims match the executed notebook outputs and temporal design choices."
**Output:** AI generated revised text for framing and memo sections tied to observed metrics and model behavior.
**Validation:** I cross-checked numbers and model naming against current notebook outputs before accepting text.
**Adaptations:** I retained only statements supported by executed outputs and removed any stale values from earlier runs.
**Final Decision:** Partially used - wording was edited for clarity and strict consistency.

## Critical Distance (specific to this lab)

AI accelerated structure and iteration, but it repeatedly reflected prior configuration until I forced re-checks after each requirement change. In this lab, that mattered because grading depends on exact consistency between executed notebook outputs and written claims. The key limitation was configuration drift: if I had accepted early text/table drafts without rerunning and revalidating outputs after each change, the submission would have had mismatched seasons, stale metrics, or incorrect model labels.
