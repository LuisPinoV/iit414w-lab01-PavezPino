# Hito 2 — README

## Quick start

Run the notebook from the repository root after installing the project dependencies:

```bash
pip install -r capstone/requirements.txt
jupyter notebook capstone/hito_2/hito2_modeling.ipynb
```

Then run all cells from top to bottom.

## Deliverables in this folder

| File | Purpose |
|---|---|
| `hito2_modeling.ipynb` | Reproducible notebook for the Hito 2 midpoint model, dual-target evaluation, what-if comparison, and error analysis. |
| `baseline_comparison.md` | Baseline comparison on `is_top10` and `is_top3`. |
| `error_analysis.md` | Slice analysis by strategy type, circuit type, and constructor tier. |
| `whatif_comparison.md` | Side-by-side strategy comparison that makes the target disagreement visible. |
| `leakage_audit.md` | Leakage and confounding guard checklist. |
| `mitigations.md` | Risks and mitigation plan for the final report. |
| `PROMPTS.md` | AI-assisted reasoning log for the expansion target and scenario validation. |

## Notebook structure

### 1. Load and split
Loads `f1_strategy_race_level.csv` and applies the locked split: train 2019–2021, calibration 2022, test 2023–2024.

### 2. Leakage guard
Separates pre-race columns, scenario inputs, audit columns, and outcomes.

### 3. Two-target modeling with stacking

**Primary target** (`is_top10`): Trains calibrated logistic regression on 20 original features. Results are locked from Hito 1.

**Expansion target** (`is_top3`): Uses **stacking** — trains calibrated logistic regression on 21 features (20 original + calibrated `is_top10` predictions as meta-feature). This approach leverages the complementary signal from the primary model while respecting the temporal split (probabilities generated separately per train/calib/test to avoid leakage).

Both models are calibrated on the 2022 validation set to ensure reliable probabilities for what-if comparisons.

### 3.5. Baseline comparisons for `is_top3`
Computes two baselines:
- **Prevalence baseline**: Constant prediction = train set positive rate
- **Grid-position rule baseline**: Domain-motivated rule (P=0.35 if grid≤5, else 0.10)

Compares both against the calibrated model to quantify improvement.

### 4. Matched what-if pair
Scores a fixed driver-race context under two strategy plans and shows where the targets disagree.

### 5. Visualization
Produces the side-by-side bar chart for the what-if pair.

### 6. Error analysis
Computes slices by strategy type, circuit type, and constructor tier for both targets.

## What changed versus Hito 1

- Hito 2 locks the expansion target to `is_top3`.
- The notebook now compares two targets side by side instead of only reporting a single baseline.
- The what-if example is chosen to surface a disagreement between `is_top10` and `is_top3`.
- Error analysis is now a first-class artifact, not an appendix.

## Notes

- The model should be read as scenario-conditioned guidance, not causal proof.
- Strategy variables are treated as what-if scenario inputs.
- Re-run the notebook after any feature or scenario change so the markdown artifacts stay aligned with the code.
