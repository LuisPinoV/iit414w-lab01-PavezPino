# PROMPTS.md — AI Usage Log
# IIT414W - Lucas Pavez - Lab 2

## Entry [1] — Data ingestion + temporal feature engineering · 2026-03-21

**Context**
I used AI assistance to draft the initial Ergast/Jolpi API ingestion and then iteratively corrected the notebook to satisfy course requirements for temporal validation, leakage prevention, and error analysis.

**Prompt(s)**
- "Build Python code to download F1 results and qualifying data for 2022–2024 and merge them by season, round, and driver."
- "Refactor features to include lag/rolling features with shift(1) before aggregation, and exclude post-race leakage fields from model input."
- "Replace random CV with season-based temporal split and keep random_state=RANDOM_SEED."
- "Add error analysis with at least 3 concrete failure cases including race/driver, hypothesis, and next step."

**Relevant Output (accepted/adapted)**
- API pagination and dataset merge for results + qualifying.
- Engineered features:
  - `driver_prev_grid` (lag)
  - `driver_top10_rate_3` (rolling with `shift(1)`)
  - `driver_q3_avg_3` (rolling with `shift(1)`)
  - `grid_quali_interaction` (interaction)
- Temporal evaluation:
  - Train on seasons `< TEST_SEASON`
  - Test on `TEST_SEASON`
  - Optional walk-forward by season without shuffling
- Leakage checklist per feature and explicit exclusion of `position`, `points`, `status`, `laps` from model inputs.
- Consistent seed usage with `RANDOM_SEED = 414` and `random_state=RANDOM_SEED`.

**Adaptations made by me**
- I reviewed and cleaned AI-generated code before use.
- I removed patterns that could cause leakage or invalid temporal validation.
- I kept only course-aligned logic and documented assumptions.

**Final decision**
AI output was used as a drafting aid and then manually corrected to match rubric requirements and reproducibility constraints.