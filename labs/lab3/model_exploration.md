# Model Exploration - [Your Name(s)]
## IIT414W - Lab 3 - Initial exploration - March 30, 2026

## 0. Framing Decision (initial - you can revise for Lab 3 final submission)
- **Business question:** How many points should we expect a driver to score in Sunday’s race based on pre-race information?
- **Target:** points
- **Metric:** MAE
- **Why this framing:** Points are continuous and ordered, so regression aligns directly with race strategy decisions where the magnitude of expected points matters, not only whether points are scored.
- **Rejected alternative:** Binary top-10 classification was rejected for now because it loses granularity between low and high point outcomes that are important for championship planning.

## 1. Models Trained
| Model | Key Hyperparameters | Features Used |
|---|---|---|
| Ridge | alpha=1.0 | grid, lags, rolling averages, circuit_id, constructor_id |
| Random Forest | n_estimators=100, max_depth=10, min_samples_leaf=5 | grid, lags, rolling averages, circuit_id, constructor_id |
| Linear Regression (OLS) | no regularization hyperparameter | grid, lags, rolling averages, circuit_id, constructor_id |

## 2. Comparison Table (same metric, same validation)
| Rank | Model | Feature Set | Validation | Train MAE | Test MAE | Gap (Test−Train) | WHY this result |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Random Forest (100, depth=10) | All engineered + categorical | 2023–2024 temporal split | 2.482 | 2.838 | +0.356 (+12.5%) | Captures nonlinearities/interactions better, with moderate overfitting risk from model complexity. |
| 2 | Grid heuristic | Grid only | 2023–2024 temporal split | — | 3.246 | — | Encodes a strong prior: starting grid position is highly related to expected points. |
| 3 | Ridge (alpha=1.0) | All engineered + categorical | 2023–2024 temporal split | 3.350 | 3.299 | -0.051 (-1.5%) | Regularized linear model; stable but can underfit nonlinear grid-to-points effects. |
| 4 | Linear Regression (OLS) | All engineered + categorical | 2023–2024 temporal split | 3.347 | 3.304 | -0.044 (-1.3%) | Your model: simple unregularized linear baseline to compare directly against Ridge and RF. |

## 3. Best Model Justification (3+ sentences)
The best model in this exploration is Random Forest (100, depth=10) based on the lowest test MAE. I also checked the train-test gap to avoid selecting a model that only memorizes historical patterns. Compared with naive baselines, the winning model captures additional structure from lag and constructor/context features, which makes it more useful for decision support than a constant prediction.

## 4. One Honest Limitation
A key limitation is that the model does not include race-day factors such as weather, incidents, penalties, or mechanical failures. As a result, predictions can be unreliable on unusual races where those factors dominate and historical averages are less informative.