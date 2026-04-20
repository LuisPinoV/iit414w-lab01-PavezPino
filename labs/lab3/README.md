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
*Python:** 3.12.3

### Setup Instructions

Requiremnts:

    - python 3.12.2 or above
    - virtual enviroment (venv)
    - vscode with jupyter extension or Jupyter Notebook installed

To create the enviroment use:

```bash
python3 -m venv venv
```
If there are errors try with py insetead python3

And to activate in **windows** use 
```bash
# For CMD
venv\Scripts\activate

#For Powershell
.\venv\Scripts\Activate.ps1

#For Git Bash
source venv/Scripts/activate
```

To activate in a Linux Distribution from a terminal:

```bash
source venv/bin/activate
```

And then, when the virtual enviroment is activated, run:

```bash
pip install -r requirements.txt
```
### Ejecution

1. Open `lab3_model_comparison.ipynb`.
2. Use a Python 3.12 environment with previous libraries installled.
4. Run all cells from top to bottom.
5. Verify outputs are visible (especially the comparison table cell).

## Reproducibility constraints used

- `RANDOM_SEED = 414` used in all stochastic components.
- Temporal split only: Train seasons 2022-2023, Test season 2024.
- Same primary metric across all rows: MAE.

## Runtime expectation

Notebook usually runs in a few minutes, depending on API response time from Ergast/Jolpi endpoints.
