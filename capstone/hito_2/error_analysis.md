# Error Analysis — Hito 2

The rubric requires slices by strategy type, circuit type, and one additional context. This analysis uses `constructor_tier` as the extra context because it is pre-race, operationally meaningful, and has clear class-balance differences.

## 1) Strategy type

| Strategy bucket | Rows | `is_top10` Brier | `is_top10` AUC | `is_top3` Brier | `is_top3` AUC | Readout |
|---|---:|---:|---:|---:|---:|---|
| `no_stop` | 15 | 0.0042 | n/a | 0.0006 | n/a | Tiny slice and one-class in the held-out set, so this is mostly a sanity check rather than a stable performance estimate. |
| `one_stop` | 353 | 0.1399 | 0.8810 | 0.0853 | 0.9227 | Reasonable but not the best slice. One-stop is harder for `is_top10` than two-stop in this dataset. |
| `two_stop` | 368 | 0.1239 | 0.9055 | 0.0762 | 0.9352 | Strongest slice overall. This is the most stable operating region for both targets. |
| `three_plus_stop` | 153 | 0.1473 | 0.8660 | 0.0782 | 0.8782 | Hardest slice for `is_top10`; `is_top3` still holds up but degrades slightly relative to two-stop. |

## 2) Circuit type

| Circuit type | Rows | `is_top10` Brier | `is_top10` AUC | `is_top3` Brier | `is_top3` AUC | Readout |
|---|---:|---:|---:|---:|---:|---|
| `permanent` | 579 | 0.1288 | 0.8949 | 0.0816 | 0.9130 | Best-balanced slice and the most representative operating regime. |
| `street` | 192 | 0.1255 | 0.9070 | 0.0794 | 0.9259 | Good discrimination and slightly better than permanent on `is_top10`. |
| `semi-street` | 118 | 0.1601 | 0.8540 | 0.0651 | 0.9572 | Weakest slice for `is_top10`, but very strong ranking on `is_top3`. The smaller sample size suggests caution. |

## 3) Additional context: constructor tier

| Constructor tier | Rows | `is_top10` Brier | `is_top10` AUC | `is_top3` Brier | `is_top3` AUC | Readout |
|---|---:|---:|---:|---:|---:|---|
| `backmarker` | 312 | 0.1268 | 0.7903 | 0.0064 | 0.8645 | Easy slice for `is_top3` because the positive class is extremely rare; `is_top10` discrimination is the weakest here. |
| `midfield` | 407 | 0.1500 | 0.8577 | 0.0897 | 0.8998 | This is the key strategic slice and the one the advisor should care about most. |
| `front` | 170 | 0.0999 | 0.7245 | 0.1861 | 0.7960 | The class balance flips here: top-3 becomes common, so calibration is harder even though the ranking problem is more concentrated. |

## Summary

- The model is strongest on `two_stop` rows and on the permanent/street circuit families.
- `is_top3` is generally easier to rank than `is_top10`, but the front-runner slice is harder to calibrate because the base rate is much higher.
- These slices support the Hito 2 story: the expansion target reveals strategy trade-offs that are invisible if you only look at `is_top10`.
