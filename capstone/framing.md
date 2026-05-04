# Team Decision Sheet — Capstone Hito 1

### IIT414W · F1 Race Strategy Advisor · Mon May 4, 2026

> **Instructions.** Complete this sheet in your team repo as `framing.md`. Every team has 60 minutes during the studio block (14:45–15:45). Required commits: by 15:00 (sections 1–4 populated) and by 15:40 (full sheet + dataset-load notebook). No section can be left blank — write "TBD with rationale" if you are uncertain, but blank entries fail the framing rubric.

**Team members:** Lucas Pavez - Luis Pino
**Team name:** Model Thinkers (11 group)
**GitHub repo URL:** [Repo](https://github.com/LuisPinoV/iit414w-lab01-PavezPino)

---

## 1. Decision Context

**What strategy decision is this tool supporting?**

Whether to recommend a one-stop or two-stop pit strategy for a driver starting in midfield positions at a high-degradation circuit.

**Who makes this decision?**

The strategy engineer during pre-race planning meetings.

**When in the race weekend is the decision made?**

Friday evening after FP2, before the car setup is locked for parc fermé conditions.

---

## 2. Target & Metric

**Target (LOCKED for Hito 1):** `is_top10`

**Primary metric:** Brier score

**Why this metric for this decision?** (2 sentences max — what does the metric measure that an alternative does not?)

Brier score evaluates the accuracy of probabilistic predictions by measuring both calibration (how well predicted probabilities match actual outcomes) and discrimination (ability to distinguish between classes). This is essential for strategy decisions where we need reliable probability estimates for risk assessment, unlike simpler metrics like accuracy that ignore prediction confidence.

**Secondary metric (optional but recommended):** ROC-AUC

**Temporal split (LOCKED for Hito 1):**

- Train: seasons 2019, 2020, 2021
- Calibration: season 2022 (used to fit calibration mapping; never for model selection)
- Test: seasons 2023, 2024 (untouched until final evaluation)

---

## 3. Baseline Plan

**Baseline approach (one sentence):**

Calibrated logistic regression using grid_position, constructor_tier, and n_stops.

**Why is this baseline F1-defendable?** (One sentence — could you justify it without ever seeing 2023–2024 data?)

These features represent fundamental pre-race performance indicators and basic strategy choices that have consistently influenced top-10 outcomes in historical F1 data.

**Direction check:** higher baseline score means higher predicted P(top10). Yes / No / Explain.

Yes, the logistic regression outputs probabilities where higher values indicate greater likelihood of top-10 finish.

**Expected baseline performance vs docent floor:**

- Grid-rule docent baseline: Brier = 0.208 on test
- Calibrated docent model: Brier = 0.132 on test, ROC-AUC = 0.892
- Our team's best baseline expected to land near: Brier = 0.140

---

## 4. What-If Comparison Plan

**Strategy variables we will vary:**

- [x] `n_stops`
- [x] `compound_sequence`
- [x] `stint_lengths` (or stint1_length, stint2_length, etc.)
- [ ] `avg_pit_stop_duration_s`
- [ ] Other: ____________________

**Concrete scenarios to compare (at least two, with specific values):**

Scenario A: n_stops=1, compound_sequence='S-M' (one-stop strategy with soft to medium tires)
Scenario B: n_stops=2, compound_sequence='S-M-S' (two-stop strategy with soft-medium-soft tires)

**Decision metric for the comparison:**

Difference in calibrated P(is_top10) between Scenario A and Scenario B, with 90% confidence interval from bootstrapping.

---

## 5. Limitations Acknowledgment

**Five known dataset limitations are documented in the Capstone Brief. Which TWO most affect our team's specific approach?**

Limitation #1 we acknowledge: Historical data from 2019-2024 may not capture future regulatory changes or technological advancements in F1.

> Why it matters for our approach (1 sentence): This could lead to overfitting on past patterns that don't generalize to future seasons with different car designs or rules.

Limitation #2 we acknowledge: Dataset lacks detailed race-day incident data such as safety car periods, crashes, or penalties that can dramatically alter race outcomes.

> Why it matters for our approach (1 sentence): Strategy recommendations based on pre-race data may be invalidated by unpredictable incidents that our model cannot anticipate.

---

## 6. Experiment Plan for Hito 1

**Three experiments we will run between today and Wednesday 16:20:**

1. Implement and evaluate the baseline calibrated logistic regression model using temporal validation.
2. Extend the baseline by incorporating additional strategy features like compound_sequence and stint_lengths.
3. Compare the extended model against a random forest ensemble to capture non-linear strategy-performance interactions.

**Hypothesis for each (one line each — what do we expect to happen and why?):**

1. The baseline will achieve Brier score around 0.140, improving over the grid-rule baseline by leveraging basic strategy features.
2. Adding strategy features will reduce Brier score by at least 0.01 due to capturing tire degradation and pit timing effects.
3. Random forest will outperform logistic regression with Brier score below 0.130 by modeling complex interactions between strategy variables and driver performance.

## 7. Team Workflow

**Who is doing what between now and Wednesday?**

| Member | Owns | Branch / file in repo |
| ------ | ---- | --------------------- |
| Lucas Pavez | Baseline implementation and experiments 1-2 | capstone/baseline_model.ipynb |
| Luis Pino | Model comparison (experiment 3) and framing documentation | capstone/model_comparison.ipynb |

**When does each member commit by?** (We need at least one commit per member per day Tue and Wed.)

Lucas commits by Tuesday 18:00 and Wednesday 16:20; Luis commits by Tuesday 20:00 and Wednesday 16:20.

---

## 8. Critique Received in Pair Review

> *Filled during Block 5 (15:45–16:05) after the partner team reviews this sheet.*

**Reviewing team:** ____________________

**Concrete critique we received:**

**How we will address this critique by Wednesday:**

---

## Self-Check Before Committing

Before you push this to GitHub, verify:

- [ ] Decision context is one sentence, not a paragraph
- [ ] Target says exactly `is_top10` (not "Top-10" or "P(top10)")
- [ ] Temporal split shows three blocks: 2019–2021 / 2022 / 2023–2024
- [ ] Baseline is described in code-realistic terms (we could implement it)
- [ ] What-if scenarios have specific feature values, not generic words
- [ ] At least 2 of the 5 limitations are acknowledged with consequence
- [ ] PROMPTS.md exists in the repo (even if empty for now — will be populated by Wednesday)
