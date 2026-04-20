# Lab 3 - Model Comparison

## Objective
Compare at least 3 models (including at least 2 baselines) for an F1 prediction task with a justified framing and temporal validation.

## Files in this folder
- `framing_decision.md`: Framing choice and justification.
- `lab3_model_comparison.ipynb`: Main notebook with executed outputs.
- `comparison_table.md`: Standalone model comparison table.
- `memo.md`: One-page technical memo for non-technical audience.
- `PROMPTS.md`: AI interaction log with traceability.
- `model_exploration.md`: In-class exploration artifact (kept unchanged).

## Reproducibility
1. Open `lab3_model_comparison.ipynb`.
2. Use a Python 3.12 environment (the repository already includes `.venv`).
3. Install dependencies if needed:
	- `pip install pandas numpy requests scikit-learn`
4. Run all cells from top to bottom.
5. Verify outputs are visible (especially the comparison table cell).

## Reproducibility constraints used
- `RANDOM_SEED = 414` used in all stochastic components.
- Temporal split only: Train seasons 2022-2023, Test season 2024.
- Same primary metric across all rows: MAE.

## Runtime expectation
Notebook usually runs in a few minutes, depending on API response time from Ergast/Jolpi endpoints.
