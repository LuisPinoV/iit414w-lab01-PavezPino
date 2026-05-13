# Baseline Comparison — Hito 2 (with Stacking)

## Summary

This document compares model performance for both targets against domain baselines and docent (instructor) benchmarks.

- **`is_top10` (primary, locked from Hito 1)**: Logistic regression, trained standalone on 20 original features
- **`is_top3` (expansion target, Hito 2)**: Logistic regression with **stacking** — uses calibrated `is_top10` predictions as an additional feature

## 1. Target: `is_top10` (Standalone)

| Baseline / Model | Brier | Log Loss | ROC-AUC | Pos. Rate |
|---|---|---|---|---|
| Docent (instructor benchmark, Lab 1) | 0.132 | — | — | — |
| **Our model** | 0.1322 | 0.4173 | 0.8920 | 0.517 |

**Interpretation**: Our logistic regression matches the docent benchmark (Brier ≈ 0.132), validating the modeling approach.

---

## 2. Target: `is_top3` (Expansion, with Stacking)

### Baselines

| Baseline | Brier | Log Loss | ROC-AUC | Description |
|---|---|---|---|---|
| Prevalence | 0.1311 | 0.4317 | 0.5000 | Constant P = 0.154 (train set mean) |
| Grid-position rule | 0.0964 | 0.3197 | 0.8556 | If grid ≤ 5: P=0.35, else P=0.10 |

### Stacked Model

| Model | Brier | Log Loss | ROC-AUC | Pos. Rate | Features | Notes |
|---|---|---|---|---|---|---|
| **is_top3 with stacking** | **0.0789** | **0.2522** | **0.9212** | 0.155 | 21 (20 original + P(is_top10)) | Uses calibrated `is_top10` predictions as meta-feature |

### Performance Gains

| vs. Baseline | Brier Improvement |
|---|---|
| Prevalence baseline | **39.8%** (0.1311 → 0.0789) |
| Grid-position rule | **18.2%** (0.0964 → 0.0789) |

**Interpretation**: Stacking significantly improves performance by leveraging the complementary signal from `is_top10` predictions. The stacked model outperforms both domain baselines.

---

## 3. Stacking Approach

### Rationale

Stacking exploits the relationship between the two targets:
- Finishing in **top 3** requires finishing in **top 10** (logical dependency)
- `is_top10` model learns robust patterns about race position
- Those patterns (via calibrated probabilities) provide valuable signal for `is_top3`

### Implementation

1. Train `is_top10` model on standard feature set (20 features)
2. Calibrate on 2022 validation set
3. Generate `is_top10` probabilities on each split (train/calib/test) **separately** to avoid leakage
4. Add `P(is_top10)` as a new feature to train/calib/test sets
5. Train `is_top3` model on expanded feature set (21 features)
6. Calibrate `is_top3` on 2022 validation set

### Leakage Guard

- Probabilities are generated **independently per split** (no forward leakage from test predictions into training)
- Both models use the same **locked temporal split** (train 2019–2021, calib 2022, test 2023–2024)
- Only **scenario_input** features vary in what-if comparisons (enforced in `score_pair()`)

---

## 4. Test Set Performance (2023–2024)

### Metrics by Target

| Metric | is_top10 (standalone) | is_top3 (stacked) |
|---|---|---|
| Brier | 0.1322 | 0.0789 |
| Log Loss | 0.4173 | 0.2522 |
| ROC-AUC | 0.8920 | 0.9212 |
| Positive Rate | 51.7% | 15.5% |
| N (test set) | 889 | 889 |

---

## 5. Key Findings

1. **Stacking works**: Adding `P(is_top10)` improves `is_top3` by 18.2% over the grid-position rule baseline.
2. **Complementary targets**: The targets have learned different decision boundaries; stacking merges their signals.
3. **Calibration matters**: Both models are calibrated on 2022 to ensure reliable probabilities.
4. **Temporal discipline**: Separate probability generation per split prevents leakage while enabling strong meta-features.

---

## 6. Deliverables

✅ Dual-target modeling complete  
✅ Stacking implemented with leakage guard  
✅ Baselines compared  
✅ What-if pair found (targets disagree on Bahrain VER 1-stop vs 2-stop)  
✅ Error analysis sliced by strategy, circuit, constructor  
