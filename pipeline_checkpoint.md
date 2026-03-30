# Pipeline Checkpoint — Pavez Pino

## 1. Pipeline architecture
- Step 1: Preprocessing with `ColumnTransformer` separates numeric and categorical features to apply the correct transformations per data type.
- Step 2: Numeric encoding/scaling uses `StandardScaler` on temporal numeric features (`grid`, lags, race count, rolling std) so logistic regression sees comparable feature scales.
- Step 3: Categorical encoding uses `OneHotEncoder(handle_unknown='ignore')` for `circuit_id` and `constructor_id` to preserve nominal categories safely.
- Step 4: Model is `LogisticRegression(random_state=414, max_iter=1000)` inside a full `Pipeline`, fit with a temporal split (train 2019–2022, test 2023–2024).

## 2. Temporal features
| Feature | Formula | Leakage check |
|---|---|---|
| `position_lag_1` | `groupby('driver_id')['position'].shift(1)` | ✅ Uses only previous race position per driver |
| `points_lag_1` | `groupby('driver_id')['points'].shift(1)` | ✅ Uses only previous race points |
| `season_race_count` | `groupby(['driver_id','season']).cumcount()` | ✅ Counts races completed up to the current row |
| `driver_grid_std_3` | `groupby('driver_id')['grid'].transform(lambda x: x.shift(1).rolling(3, min_periods=2).std())` | ✅ Rolling variability built from prior races only |
| `grid` | Raw starting position | ✅ Available pre-race, no future label use |

## 3. Performance comparison (same metric, same validation)
| Model | F1 (Top-10) | Precision | Recall | Notes |
|---|---:|---:|---:|---|
| Majority class | 0.6721 | 0.5061 | 1.0000 | DummyClassifier |
| Domain heuristic | 0.7899 | 0.7899 | 0.7899 | `grid ≤ 10` |
| Pipeline model | 0.8078 | 0.7974 | 0.8184 | Full pipeline (engineered features + categories) |

## 4. Interpretation
The full pipeline achieved the best Top-10 F1 (`0.8078`), beating the strongest non-pipeline baseline by `+0.0179` under the same temporal split and metric, so the gain is a fair improvement rather than an evaluation artifact. The results suggest that adding safe temporal recency/consistency features plus categorical context contributes useful signal beyond a one-feature rule (`grid ≤ 10`). Next, I would test walk-forward folds for robustness and run feature ablations to confirm which engineered features drive most of the gain.
