# Baseline Comparison — Hito 2

This table compares the two required targets under the locked split (train 2019–2021 / calibration 2022 / test 2023–2024).

| Target | Model result on test | Baseline used for comparison | Baseline result | Delta / note |
|---|---|---|---|---|
| `is_top10` | Brier = 0.1322, ROC-AUC = 0.8920, Log loss = 0.4173 | Docent benchmark from Hito 1 | Brier = 0.1320, ROC-AUC = 0.8920 | Essentially parity on the reference target; the model is calibrated but does not materially beat the docent baseline. |
| `is_top3` | Brier = 0.0789, ROC-AUC = 0.9212, Log loss = 0.2522 | Prevalence baseline: constant `P(is_top3)` equal to the training-set positive rate | Brier = 0.1311, Log loss = 0.4317 | Strong improvement over the no-signal comparator. A constant baseline is the appropriate comparison here because there is no docent benchmark for `is_top3`. |

## Notes

- The `is_top10` comparison uses the docent benchmark because that is the locked reference from Hito 1.
- The `is_top3` comparison uses a prevalence baseline because the task requires a second target but no external benchmark is provided.
- The notebook reports calibrated probabilities, so Brier score is the main quality measure for both targets.
- ROC-AUC is included to show ranking quality, but the calibration-oriented comparison is the stronger signal for this deliverable.
