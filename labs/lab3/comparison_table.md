# Model Comparison Table (Lab 3)

Primary metric for all rows: **MAE (lower is better)**  
Validation for all rows: **Temporal holdout (Train 2022-2023 -> Test 2024)**

| Rank | Model | Validation | Train MAE | Test MAE | Gap (Test-Train) | WHY this result |
|---|---|---|---:|---:|---:|---|
| 1 | Random Forest (300 trees) | Train 2022-2023 -> Test 2024 | 1.789 | 2.760 | +0.971 | Captures nonlinear interactions among grid, recent form, and circuit/constructor context. Large positive gap suggests some overfitting risk. |
| 2 | Baseline 2 - Grid heuristic | Train 2022-2023 -> Test 2024 | 3.471 | 3.092 | -0.379 | Strong domain prior: starting grid is highly related to final points. Very simple and surprisingly competitive baseline. |
| 3 | Ridge (alpha=1.0) | Train 2022-2023 -> Test 2024 | 3.119 | 3.377 | +0.258 | Regularized linear model is stable with sparse one-hot features, but still misses important nonlinear effects. |
| 4 | Linear Regression | Train 2022-2023 -> Test 2024 | 3.117 | 3.399 | +0.282 | Similar behavior to Ridge with slightly worse generalization; linear assumptions limit performance on race outcomes. |
| 5 | Baseline 1 - Global mean points | Train 2022-2023 -> Test 2024 | 5.917 | 5.912 | -0.006 | Constant prediction baseline ignores race context and driver/constructor variation. Serves as minimum performance floor. |

## Best model justification
Random Forest achieved the best test MAE (2.760), clearly outperforming both linear models and both baselines. This is consistent with nonlinear F1 dynamics where the impact of features like grid position and recent form is not strictly linear. However, the train-test gap (+0.971) indicates moderate overfitting risk, so this model is best for current deployment but should be monitored with future seasons and possibly regularized/tuned further.

## Honest limitation
The current feature set excludes race-day uncertainty (weather changes, incidents, penalties, and mechanical failures). This means the model can still miss unusual race outcomes even if average MAE is good.
