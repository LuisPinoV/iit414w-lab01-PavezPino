# Hito 2 — Final Status Report

**Date**: January 2025  
**Status**: ✅ **COMPLETE**  
**Primary Deliverable**: Dual-target F1 race strategy advisor with stacking

---

## Executive Summary

Successfully completed Hito 2 midpoint model deliverable with:
- ✅ Locked primary target (`is_top10`) from Hito 1
- ✅ Expansion target (`is_top3`) with stacking ensemble
- ✅ Calibrated logistic regression for both targets
- ✅ What-if pair showing target disagreement
- ✅ Error analysis by strategy/circuit/constructor
- ✅ Baseline comparisons (prevalence + domain rule)
- ✅ Leakage guard and temporal discipline
- ✅ Full reproducible notebook

---

## Deliverables

### Notebook
- **File**: `hito2_modeling.ipynb`
- **Cells**: 17 cells (8 code, 9 markdown)
- **Status**: All cells execute cleanly, validated outputs

### Documentation

| Document | Purpose | Key Content |
|---|---|---|
| `baseline_comparison.md` | Primary deliverable | is_top10 vs is_top3 Brier scores, baseline comparisons |
| `baseline_comparison_stacked.md` | Stacking details | 39.8% improvement over prevalence, 18.2% over grid-rule |
| `stacking_summary.md` | Comprehensive guide | Implementation, leakage guard, slice analysis, rationale |
| `error_analysis.md` | Slice analysis | Strategy/circuit/constructor tables for both targets |
| `whatif_comparison.md` | Scenario example | Bahrain VER 1-stop vs 2-stop disagreement |
| `leakage_audit.md` | Safety checklist | Column classification, no post-race features in what-if |
| `mitigations.md` | Risk assessment | Known confounds (non-random strategies, small slices, drift) |
| `PROMPTS.md` | AI reasoning log | 5 interactions documenting modeling decisions |
| `README.md` | Quick start guide | Installation, notebook structure, stacking explanation |

### Data
- **Dataset**: `f1_strategy_race_level.csv` (2447 rows, 47 columns)
- **Features**: 20 original + 1 stacked (21 total for is_top3)
- **Targets**: is_top10 (51.7% positive rate), is_top3 (15.5% positive rate)
- **Split**: Train 2019–2021 (1132), Calib 2022 (426), Test 2023–2024 (889)

---

## Key Results

### Test Set Performance (2023–2024)

**is_top10** (primary, locked):
- Brier: 0.1322 (matches docent = 0.132 ✓)
- Log Loss: 0.4173
- ROC-AUC: 0.8920
- Features: 20 original

**is_top3** (expansion, stacked):
- Brier: 0.0789 (best)
- Log Loss: 0.2522
- ROC-AUC: 0.9212
- Features: 20 original + P(is_top10)

### Baseline Comparison

**is_top3 vs baselines:**
- Prevalence (P=0.154): Brier 0.1311 → **39.8% improvement**
- Grid-position rule: Brier 0.0964 → **18.2% improvement**

### What-If Disagreement

**Context**: 2023 Bahrain, VER (grid 1)
- 1-stop S-M: P(is_top10)=0.955, P(is_top3)=0.906 → **is_top3 prefers**
- 2-stop S-M-S: P(is_top10)=0.968, P(is_top3)=0.835 → **is_top10 prefers**

**Verdict**: DISAGREE (justifies dual-target modeling)

### Error Analysis Highlights

**Best slices** (lowest Brier):
- Semi-street circuits: 0.0651 (is_top3)
- Backmarker constructors: 0.0064 (is_top3, but small sample)

**Most uncertain slices**:
- Front-row constructors: Brier 0.186 (is_top3) — small sample
- Three+-stop strategies: Brier 0.078 (is_top3)

---

## Technical Achievements

### 1. Stacking Implementation
✅ Trained is_top10 first  
✅ Generated calibrated probabilities independently per split  
✅ Added P(is_top10) as meta-feature to is_top3  
✅ Maintained strict temporal discipline  
✅ No forward leakage, no target leakage

### 2. Calibration
✅ Both models calibrated on 2022 validation set  
✅ CalibratedClassifierCV with sigmoid method  
✅ FrozenEstimator wrapper for sklearn 1.8+ compatibility  
✅ Probabilities interpretable and well-scaled

### 3. Leakage Guard
✅ Column classification: pre_race, scenario_input, audit, outcome  
✅ What-if comparisons use only scenario inputs  
✅ No post-race features in baseline  
✅ No test set leakage into training

### 4. Reproducibility
✅ Fixed random seed (414)  
✅ Locked temporal split  
✅ Feature list documented  
✅ All cells runnable from top to bottom

---

## Validation Results

| Requirement | Status | Evidence |
|---|---|---|
| Primary target locked | ✅ | is_top10 Brier=0.1322 matches docent |
| Expansion target implemented | ✅ | is_top3 with stacking, Brier=0.0789 |
| Baselines computed | ✅ | Prevalence + grid-rule, 39.8% and 18.2% improvements |
| What-if pair found | ✅ | Bahrain VER shows target disagreement |
| Error slices created | ✅ | Tables for strategy/circuit/constructor |
| Leakage guarded | ✅ | Column classification, scenario-only what-if |
| Notebook runs clean | ✅ | All 8 code cells executed, outputs match |
| Markdown artifacts aligned | ✅ | Baseline_comparison, error_analysis, etc. consistent |

---

## Files Modified/Created This Session

**Modified:**
- `hito2_modeling.ipynb`: Added stacking to Step 3
- `PROMPTS.md`: Added Interaction 5 (stacking rationale)
- `README.md`: Expanded Step 3 explanation

**Created:**
- `baseline_comparison_stacked.md`: Stacking results with improvements
- `stacking_summary.md`: Comprehensive stacking guide

---

## How to Use

### Run the notebook
```bash
cd /capstone/hito_2
jupyter notebook hito2_modeling.ipynb
# Run all cells (Ctrl+Shift+Enter or Shift+Enter per cell)
```

### Regenerate outputs
```bash
# After any feature change, re-run the notebook to update:
#   - baseline_comparison_stacked.md
#   - error_analysis.md
#   - whatif_comparison.md
```

### Key cells to understand
- **Cell 8** (Step 3): Stacking implementation — is_top10 → P_is_top10 → is_top3
- **Cell 10** (Step 3.5): Baseline comparisons for is_top3
- **Cell 12** (Step 4): What-if pair scoring with stacking
- **Cell 16** (Step 6): Error analysis by slice

---

## Known Limitations & Mitigations

| Limitation | Mitigation |
|---|---|
| Strategy may not be random (confounding) | Treat model as scenario-conditioned guidance, not causal proof |
| Small slices → high variance | Report error bars (future), aggregate when possible |
| Probability drift over time | Monitor calibration on new seasons |
| Only one what-if pair shown | Extend analysis to other races/drivers (future) |
| Stacking assumes correlation persists | Validate on future data before production deployment |

---

## Future Work

1. **Extend what-if pairs**: Search across multiple seasons/drivers for more disagreement examples
2. **Feature importance**: Analyze which features drive the stacking improvement
3. **Other targets**: Apply stacking to is_dnf, is_pole, etc.
4. **Ensemble methods**: Compare vs other ensemble techniques (weighted average, RF, XGBoost)
5. **Monitoring**: Set up monitoring dashboard for probability calibration drift
6. **User study**: Test whether teams find the dual-target guidance actionable

---

## Sign-Off

✅ **Hito 2 is ready for review.**

All deliverables are in `/capstone/hito_2/`:
- Notebook: hito2_modeling.ipynb
- Report artifacts: baseline_comparison_stacked.md, error_analysis.md, whatif_comparison.md, etc.
- Implementation guide: stacking_summary.md
- Reasoning log: PROMPTS.md

---

**Questions or feedback?** See PROMPTS.md (Interaction 5) for stacking implementation details, or stacking_summary.md for comprehensive technical documentation.
