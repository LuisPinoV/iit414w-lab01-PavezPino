# Hito 1 — README

## Quick start

```bash
# 1. Install dependencies (from repository root)
pip install -r capstone/requirements.txt

# 2. Run the notebook
jupyter notebook capstone/hito1.ipynb
# or
cd capstone && jupyter notebook hito1.ipynb && cd ..

# 3. Run all cells (Kernel → Restart & Run All)
```

## Files

| File | Purpose |
|------|---------|
| `framing.md` | Team decision sheet: decision context, baseline plan, what-if scenarios, limitations, experiment plan |
| `hito1.ipynb` | Executable baseline notebook: temporal split (train/calib/test), logistic regression baseline, calibration, metrics (Brier, log loss), calibration curve, leakage audit, scenario evaluation |
| `PROMPTS.md` | AI interaction log with 6-field documentation (Context, Prompts, Output, Validation, Adaptations, Final Decision) |
| `f1_strategy_race_level.csv` | F1 race-level dataset with strategy and outcome columns |
| `requirements.txt` | Python dependencies |

## Notebook structure

### 1. **Imports** 
Loads NumPy, pandas, sklearn, matplotlib for preprocessing, modeling, calibration, and metrics.

### 2. **Load dataset**
Reads `f1_strategy_race_level.csv` and displays shape and column names.

### 3. **Temporal split**
Implements the locked split:
- Train: 2019, 2020, 2021
- Calibration: 2022 (used only for calibration mapping)
- Test: 2023, 2024 (untouched until final evaluation)

### 4. **Baseline features & leakage audit**
Selects pre-race features:
- `grid_position` (starting grid)
- `constructor_avg_finish_pos_5race_rolling` (team form)
- `wet_day` (weather proxy)

Strategy columns (n_stops, compound_sequence, stint_lengths) are NOT used in baseline; reserved for scenario evaluation.

### 5. **Preprocessing & baseline model**
- Imputes missing numeric values (median strategy)
- Scales numeric features with `StandardScaler`
- Fits logistic regression on train split
- Calibrates on 2022 block using isotonic scaling

### 6. **Evaluate on test set**
Reports:
- **Brier score**: primary metric (lower is better)
- **Log loss**: secondary metric
- **ROC-AUC**: discrimination measure
- **Calibration curve**: visual check of calibration quality

### 7. **Leakage audit**
Explicitly lists and categorizes all dataset columns:
- Pre-race (baseline features)
- Strategy/scenario columns
- Audit/incident columns

Ensures no post-race information leaked into baseline.

### 8. **What-if scenario evaluation**
Computes calibrated P(is_top10) for three scenarios from `framing.md`:
- **Scenario A**: grid=12, constructor=8.5, n_stops=1, wet_day=0 (controlled baseline)
- **Scenario B**: grid=12, constructor=8.5, n_stops=2, wet_day=0 (strategy change)
- **Scenario C**: grid=12, constructor=8.5, n_stops=2, wet_day=1 (context shift)

Reports delta P(A) - P(B) to compare strategy effects.

## Key results

After running all cells, you will see:
- Temporal split sizes (train/calib/test)
- Baseline feature list
- Model training confirmation
- Test-set Brier score (compare vs docent floor 0.208 or calibrated docent 0.132)
- Calibration curve plot
- Leakage audit JSON
- Scenario probabilities and deltas

## Dependencies

Install with:
```bash
pip install -r capstone/requirements.txt
```

Or manually:
```bash
pip install numpy pandas scikit-learn matplotlib seaborn
```

Minimum versions:
- pandas >= 1.0
- scikit-learn >= 0.24
- numpy >= 1.19
- matplotlib >= 3.0

## How to extend for Hito 2

1. **Bootstrap confidence intervals**: Modify the scenario evaluation cell to compute bootstrap resamples of calibrated P(is_top10) and report 90% CIs on delta.
2. **Random forest comparison**: Add a second model cell fitting `RandomForestClassifier` on the same temporal split and comparing Brier/AUC.
3. **Stratified sensitivity**: Evaluate scenarios across subgroups (e.g., midfield vs non-midfield grid positions).
4. **Fallback policy**: Implement explicit decision rules if model Brier stays near docent baseline.

## Common issues

**Q: Notebook fails at "No season/year column found"**
A: The dataset column names differ. Check the actual column name with `df.columns.tolist()` in an earlier cell and update the temporal split cell accordingly.

**Q: Calibration curve is erratic**
A: The calibration block (2022) may be too small. This is acceptable; the plot documents the observed calibration on the available data.

**Q: Brier score is worse than the docent baseline**
A: This is possible depending on the dataset and features available. Document in `framing.md` why your approach was chosen, and explain the result honestly in Hito 2. A fallback policy demonstrates professionalism.

## Contact

For questions, see `framing.md` Section 7 (Team Workflow) for team member responsibilities.
