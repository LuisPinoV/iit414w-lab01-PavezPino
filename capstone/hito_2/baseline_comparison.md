# Baseline Comparison — Hito 2

This table compares the two required targets under the locked split (train 2019–2021 / calibration 2022 / test 2023–2024).

| Target | Model result on test | Baseline used for comparison | Baseline result | Delta / note |
|---|---|---|---|---|
| `is_top10` | Brier = 0.1322, ROC-AUC = 0.8920, Log loss = 0.4173 | Docent benchmark from Hito 1 | Brier = 0.1320, ROC-AUC = 0.8920 | Essentially parity on the reference target; the model is calibrated but does not materially beat the docent baseline. |
| `is_top3` | Brier = 0.0789, ROC-AUC = 0.9212, Log loss = 0.2522 | **Comparison 1**: Prevalence baseline (constant `P(is_top3)` = 0.154) | Brier = 0.1311, ROC-AUC = 0.5000, Log loss = 0.4317 | Strong improvement: **39.8% better** Brier score than prevalence. |
| `is_top3` | Brier = 0.0789, ROC-AUC = 0.9212, Log loss = 0.2522 | **Comparison 2**: Grid-position rule baseline (P=0.35 if grid ≤5, else 0.10) | Brier = 0.0964, ROC-AUC = 0.8556, Log loss = 0.3197 | Solid improvement: **18.2% better** Brier score than rule-based comparator. The rule is effective but the calibrated model adds value through richer feature interactions. |

## Notes

- The `is_top10` comparison uses the docent benchmark because that is the locked reference from Hito 1.
- The `is_top3` model is compared against two baselines:
  - **Prevalence**: The baseline of no signal (predict the mean).
  - **Grid-position rule**: A simple operational rule that leverages the strong correlation between starting position and podium likelihood in F1. This represents what a domain expert might use without machine learning.
- The notebook reports calibrated probabilities, so Brier score is the main quality measure for both targets.
- ROC-AUC is included to show ranking quality; both baselines and the model succeed on ranking, but the calibrated model achieves superior probability calibration (lower Brier and log loss).
