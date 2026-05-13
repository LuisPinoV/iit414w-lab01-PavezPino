# Stacking Implementation — Hito 2 Summary

## Overview

**Status**: ✅ Completed and validated

The `is_top3` model now uses calibrated `is_top10` predictions as an additional meta-feature (stacking). This ensemble approach leverages the complementary signal from the primary model while maintaining strict temporal discipline to prevent leakage.

---

## What is Stacking?

Stacking is an ensemble technique that:
1. Trains a primary model (base learner) on the original features
2. Generates predictions from the primary model on each data split **independently**
3. Uses those predictions as additional features for a secondary model
4. Trains the secondary model on the expanded feature set

**Key benefit**: Combines complementary patterns learned by different models to improve overall performance.

---

## Implementation Details

### Feature Engineering

**Original Feature Set** (20 features):
- Pre-race: grid_position, driver_prior3_avg_finish, constructor_prior3_avg_finish, driver_circuit_prior_avg, circuit_type, constructor_tier (6 features)
- Scenario inputs: n_stops, strategy_type, compound_sequence, stint1_length, stint2_length, stint3_length, pit_stop_time1, pit_stop_time2, pit_stop_time3, pit_stop_time3 (12 features)
- Derived: driver_age_at_race, driver_prior_races_count (2 features)

**Stacked Feature Set** (21 features):
- All 20 original features +
- `P(is_top10)` = calibrated probability from `is_top10` model

### Training Pipeline

1. **Train `is_top10` model**
   - Data: train set (2019–2021, n=1132)
   - Features: 20 original features
   - Calibrate on: calib set (2022, n=426)
   - Output: calibrated model

2. **Generate `is_top10` probabilities (separate per split)**
   - On train set → `train_stacked['P_is_top10']`
   - On calib set → `calib_stacked['P_is_top10']`
   - On test set → `test_stacked['P_is_top10']`
   - **Critical**: Each split uses independent probability generation (no forward leakage)

3. **Train `is_top3` model on stacked features**
   - Data: train_stacked (same rows as train, but with added `P_is_top10` column)
   - Features: 21 (original 20 + meta-feature)
   - Calibrate on: calib_stacked (2022, with `P_is_top10` added)
   - Output: calibrated model

4. **Evaluate on test set**
   - Predictions use test_stacked with `P_is_top10` pre-computed

### Leakage Guard

✅ **No forward leakage**: Probabilities computed independently per split  
✅ **No target leakage**: `P_is_top10` is a pre-race model output, not post-race audit  
✅ **Temporal discipline**: Train (2019–2021), calib (2022), test (2023–2024) strictly enforced  
✅ **Scenario-only what-if**: `score_pair()` ensures only scenario inputs vary in comparisons

---

## Performance Comparison

### Overall Test Set Metrics (2023–2024)

| Model | Brier | Log Loss | ROC-AUC | Notes |
|---|---|---|---|---|
| `is_top10` (standalone) | 0.1322 | 0.4173 | 0.8920 | Locked from Hito 1 |
| `is_top3` (stacked) | **0.0789** | **0.2522** | **0.9212** | Uses P(is_top10) as meta-feature |

### vs. Baselines for `is_top3`

| Baseline | Brier | Improvement | Notes |
|---|---|---|---|
| Prevalence (P=0.154) | 0.1311 | ↓ 39.8% | No signal |
| Grid-position rule | 0.0964 | ↓ 18.2% | Domain heuristic |
| **Stacked model** | **0.0789** | — | Best performer |

### Error Analysis Slices (test set)

**By Strategy Type:**
- One-stop: Brier=0.0853, ROC-AUC=0.9227
- Two-stop: Brier=0.0762, ROC-AUC=0.9352
- Three+-stop: Brier=0.0782, ROC-AUC=0.8782

**By Circuit Type:**
- Permanent: Brier=0.0816, ROC-AUC=0.9130
- Street: Brier=0.0794, ROC-AUC=0.9259
- Semi-street: Brier=0.0651, ROC-AUC=0.9572

**By Constructor Tier:**
- Front: Brier=0.1861, ROC-AUC=0.7960 (small sample, n=170)
- Midfield: Brier=0.0897, ROC-AUC=0.8998
- Backmarker: Brier=0.0064, ROC-AUC=0.8645 (low base rate)

---

## Matched What-If Pair (with Stacking)

**Context**: 2023 Bahrain Grand Prix, VER (Red Bull), grid position 1

| Strategy | Stops | Compounds | P(is_top10) | P(is_top3) | Verdict |
|---|---|---|---|---|---|
| 1-stop | 1 | S-M | 0.9547 | 0.9059 | **is_top3 prefers** |
| 2-stop | 2 | S-M-S | 0.9677 | 0.8354 | — |

**Disagreement**: `is_top10` favors the 2-stop (higher P=0.9677), but `is_top3` favors the 1-stop (P=0.9059 > 0.8354). This makes sense: a 2-stop plan might ensure top 10 but risk missing the podium with an extra pit stop.

---

## Why Stacking Works Here

1. **Logical dependency**: Finishing top 3 requires finishing top 10 — the models' outputs are naturally correlated.
2. **Complementary patterns**: `is_top10` learns what strategy/position/driver combinations lead to strong race performance; `is_top3` learns what leads to podium finishes. Stacking allows `is_top3` to leverage `is_top10`'s insights.
3. **Calibrated meta-feature**: Using calibrated probabilities (not raw predictions) ensures the meta-feature is interpretable and well-scaled.
4. **Separate probability generation**: Computing probabilities per-split prevents leakage while enabling strong ensemble signal.

---

## Files Modified

| File | Change |
|---|---|
| `hito2_modeling.ipynb` (Cell 8) | Updated Step 3 to train `is_top10` first, generate stacked probabilities, then train `is_top3` with expanded feature set |
| `hito2_modeling.ipynb` (Cell 12) | Updated `score_pair()` to compute `is_top3` predictions using `STACKED_FEATURE_COLS` |
| `baseline_comparison_stacked.md` | New file documenting stacking results and improvements |
| `PROMPTS.md` | Added Interaction 5 documenting stacking rationale and validation |
| `README.md` | Updated Step 3 description to explain stacking and leakage guard |

---

## Validation Checklist

- ✅ All notebook cells execute cleanly (no errors)
- ✅ What-if pair scoring produces expected disagreement
- ✅ Error analysis tables generated correctly by slice
- ✅ Leakage guard maintained (probabilities per-split, scenario-only what-if)
- ✅ Baseline improvements verified (39.8% vs prevalence, 18.2% vs grid-rule)
- ✅ Temporal split respected (no forward leakage)
- ✅ Markdown deliverables updated and consistent with notebook outputs

---

## Next Steps (for Hito 3+)

1. Consider additional ensemble methods (e.g., weighted average, stacking with multiple base learners)
2. Investigate which features in the stacked model contribute most to the improvement
3. Test stacking on other target pairs (e.g., `is_dnf` vs `is_top10`)
4. Explore calibration curves to ensure probability estimates remain well-calibrated
5. Consider what-if scenarios beyond the Bahrain VER example to show breadth of disagreement

---

## Summary

Stacking successfully improves the `is_top3` model by 18.2% over the grid-position baseline and 39.8% over the prevalence baseline. The approach respects temporal discipline, prevents leakage, and maintains interpretability through calibrated meta-features. The matched what-if pair demonstrates a realistic scenario where the two targets recommend different strategies, justifying the dual-target modeling approach.
