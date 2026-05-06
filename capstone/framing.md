# Team Decision Sheet — Capstone Hito 1

### IIT414W · F1 Race Strategy Advisor · Mon May 4, 2026

> *Instructions.* Complete this sheet in your team repo as framing.md. Every team has 60 minutes during the studio block (14:45–15:45). Required commits: by 15:00 (sections 1–4 populated) and by 15:40 (full sheet + dataset-load notebook). No section can be left blank — write "TBD with rationale" if you are uncertain, but blank entries fail the framing rubric.

*Team members:* Lucas Pavez - Luis Pino
*Team name:* Model Thinkers (11 group)
*GitHub repo URL:* [Repo](https://github.com/LuisPinoV/iit414w-lab01-PavezPino)

---

## 1. Decision Context

*What strategy decision is this tool supporting?*

Whether to recommend a one-stop or two-stop pit strategy that maximizes probability of scoring points (top-10) for a midfield-starting driver at a high-degradation circuit.

*Who makes this decision?*

The strategy engineer during pre-race planning meetings.

*When in the race weekend is the decision made?*

Friday evening after FP2, before the car setup is locked for parc fermé conditions.

---

## 2. Target & Metric

*Target (LOCKED for Hito 1):* is_top10

*Primary metric:* Brier score

*Why this metric for this decision?* (2 sentences max — what does the metric measure that an alternative does not?)

Because bries score punish higher the errors with overconfidence, so this errors that could be translate at risk strategies should be avoided.

*Secondary metric (optional but recommended):* ROC-AUC

*Temporal split (LOCKED for Hito 1):*

- Train: seasons 2019, 2020, 2021
- Calibration: season 2022 (used to fit calibration mapping; never for model selection)
- Test: seasons 2023, 2024 (untouched until final evaluation)

---

## 3. Baseline Plan

*Baseline approach (one sentence):*

Calibrated logistic regression using pre-race features (grid_position, constructor_tier, wet_day forecast proxy), while strategy variables are evaluated only through controlled what-if scenarios.

*Why is this baseline F1-defendable?* (One sentence — could you justify it without ever seeing 2023–2024 data?)

These are decision-time signals available before race start, making the baseline operationally realistic and less vulnerable to post-race leakage.

*Direction check:* higher baseline score means higher predicted P(top10). Yes / No / Explain.

Yes, the logistic regression outputs probabilities where higher values indicate greater likelihood of top-10 finish.

*Expected baseline performance vs docent floor:*

- Grid-rule docent baseline: Brier = 0.208 on test
- Calibrated docent model: Brier = 0.132 on test, ROC-AUC = 0.892
- Our team's best baseline expected to land near: Brier = 0.140

---

## 4. What-If Comparison Plan

*Strategy variables we will vary:*

- [x] n_stops
- [x] compound_sequence
- [x] stint_lengths (or stint1_length, stint2_length, etc.)
- [ ] avg_pit_stop_duration_s
- [x] Other: scenario context fixed (grid_position, constructor_tier, wet_day)

*Concrete scenarios to compare (at least two, with specific values):*

Scenario A (controlled baseline): grid_position=12, constructor_tier='mid', wet_day=0, avg_pit_stop_duration_s=22, n_stops=1, compound_sequence='S-M', stint_lengths=[40].

Scenario B (single-variable-change on strategy package): grid_position=12, constructor_tier='mid', wet_day=0, avg_pit_stop_duration_s=22, n_stops=2, compound_sequence='S-M-S', stint_lengths=[22,20].

Scenario C (context-shift sensitivity check): grid_position=12, constructor_tier='mid', wet_day=1, avg_pit_stop_duration_s=26, n_stops=2, compound_sequence='S-M-S', stint_lengths=[20,18].

*Decision metric for the comparison:*

Primary: difference in calibrated P(is_top10) between Scenario A and B, with 90% bootstrap confidence interval.

Secondary: expected finishing position proxy difference for tie-breaks when top-10 probabilities are close.

---

## 5. Limitations Acknowledgment

*Five known dataset limitations are documented in the Capstone Brief. Which TWO most affect our team's specific approach?*

Limitation #1 we acknowledge: Strategy features (`n_stops`, `compound_sequence`, `stint_lengths`) are observed post-race and are not direct pre-race predictors.

> Why it matters for our approach (1 sentence): We must treat these variables as counterfactual scenario inputs, otherwise model performance would be inflated by leakage and not deployable for Friday planning.

Limitation #2 we acknowledge: Strategy choice is confounded with latent factors (car pace, driver quality, weather, and incidents), so strategy-outcome associations are not purely causal.

> Why it matters for our approach (1 sentence): Estimated effects of n_stops and tire plans may reflect hidden performance differences rather than pure strategy impact, so recommendations must be framed as associative scenario guidance.

---

## 6. Experiment Plan for Hito 1

*Three experiments we will run between today and Wednesday 16:20:*

1. Implement and evaluate the baseline calibrated logistic regression model using temporal validation.
2. Run controlled what-if scenario pairs (A vs B) and context-shift sensitivity (A/B vs C) with bootstrap confidence intervals.
3. Compare logistic regression with a random forest model, then apply a fallback policy if discrimination/calibration does not clearly improve.

*Hypothesis for each (one line each — what do we expect to happen and why?):*

1. The pre-race baseline will beat the grid-rule baseline by improving calibration without relying on post-race strategy leakage.
2. Controlled scenario comparisons will show stable direction in delta P(is_top10) when only strategy changes under fixed context.
3. If model gains are marginal, calibrated scenario ranking with uncertainty bounds will still provide a defensible recommendation policy.

## 7. Team Workflow

*Who is doing what between now and Wednesday?*

| Member | Owns | Branch / file in repo |
| ------ | ---- | --------------------- |
| Lucas Pavez | Baseline implementation and experiments 1-2 | capstone/baseline_model.ipynb |
| Luis Pino | Model comparison (experiment 3) and framing documentation | capstone/model_comparison.ipynb |

*When does each member commit by?* (We need at least one commit per member per day Tue and Wed.)

Lucas commits by Tuesday 18:00 and Wednesday 16:20; Luis commits by Tuesday 20:00 and Wednesday 16:20.

---

## 8. Critique Received in Pair Review

> Filled during Block 5 (15:45–16:05) after the partner team reviews this sheet.

*Reviewing team:* TheUltrakills

### Q1
From looking at their decision and target, we came to the conclusion that it lacked a bit of direction, as its not clear what the end goal is for the midfield driver. If it is to get on the podium, then the target is to coarse. But, if their goal is to get points on the board, then the target is a great match (as you score when in top 10).

TIP: Pit-stop choices often shift finishing position by a few places; expected points or an ordinal/multi-class target captures that nuance better.

#### answer
We will clarify the decision objective as "maximize probability of scoring points (top-10) for a midfield driver" and keep `is_top10` as the locked Hito 1 target. As a methodological improvement, we will add a secondary analysis metric (expected finishing position proxy) to check whether strategy differences are still meaningful when two scenarios have similar top-10 probability.

### Q3
> Their scenarios remain too generic and too similar between each other:
- They lack depth, as curreently they just change the amount of pit stops and compund sequence.
- They work from the base 2 variables, and because of that they lack more insight as to what a real scenario could bring.

- They do have specific values, which is good!

Suggestions: They have a good base, but the lack of depth is hurting. Therefore, we sugest the following changes and additions:
- Scenario A: n_stops=1, compound_sequence='S-M', stint_lengths=[40], avg_pit_stop_duration_s=22, grid_position=12, constructor_tier='mid' (one-stop: soft→medium; single long stint of 40 laps)
- Scenario B: n_stops=2, compound_sequence='S-M-S', stint_lengths=[22,20], avg_pit_stop_duration_s=22, grid_position=12, constructor_tier='mid' (two-stop: soft→medium→soft; stints 22 + 20 laps)

TIP: include the same temporal/covariate context (e.g., grid_position, constructor_tier, avg_pit_stop_duration_s) so scenarios are executable. For point-sensitive decisions consider adding an expected-points scenario or a is_podium alternative.

#### answer
We will convert the what-if design into controlled comparisons from a fixed baseline row (same driver profile, circuit type, grid position, constructor tier, and weather context). Methodologically, we will run two paired tests: (1) isolate `n_stops` effect with all other covariates fixed, and (2) isolate compound/stint structure with fixed `n_stops`, then report delta calibrated P(is_top10) with bootstrap confidence intervals.

### Q4
They did well adressing future changes to F1 races, but missed the present cross dependance of these variables, as your baseline uses n_stops, compound_sequence, and stint_lengths as features to predict is_top10. 
> Remember, strategy choices are NOT random. They're made by strategy engineers who know the car performance, driver skill, weather, and track conditions. Better cars/drivers with better conditions tend to choose 1-stop and also tend to finish top-10 (these events are dependant on each other). This is also important to note as note of the mistakes you made in Section 4 (your lack of depth made it so you didn't acount for these cross-dependencies).

Suggestions: we reccomend that you choose between these options
1. Acknowledge the confounder in Section 5 and discuss how you'll handle it (matching, stratification, causal forests, etc.).
2. Pivot to using only pre-race observable features (weather forecast, circuit characteristics, grid position) and drop strategy features from the model.
3. Clearly frame your "what-ifss" as a descriptive correlation ("historically, 1-stop drivers finished top-10 X% of the time") not a causal effect ("recommending 1-stop will increase P(top10) by X%").

TIP: Limitation (4), consider that strategy features are post-race observations (this also hits your criteria): if n_stops and compound_sequence are known only after the race, how can you use them in a Friday pre-race model? You should clarify whether you're treating these as hypothetical counterfactuals ("if we commit to 1-stop...") or post-hoc descriptive features.

#### answer
We will explicitly treat strategy variables as scenario inputs (counterfactual settings), not causal proof from historical outcomes. To reduce confounding risk, we will add control covariates (grid position, constructor tier, wet flag, and circuit characteristics where available), evaluate sensitivity across strata (e.g., midfield grid bands), and state that recommendations are associative unless supported by controlled scenario stability.

### critique
> Your section 4 doesn't consider the cross dependancy between the dataset features, which in turn affects your later sections. You dont specify important variables such as driver, circuit, weather or compound. This, in turn, will lead to not having proper "what-ifs" for your first deliverable, and also may get a wrong understanding of following criteria. 
We reccomend that you shuffle the dataset and pick specific values for your scenarios (ex: constructor/team, driver, weather), that way you get more depth and can get a great result on the first deliverable.

#### answer
Improvement method to implement immediately:
1. Replace generic Scenario A/B with fully specified, reproducible scenario rows (including grid position, constructor tier, wet/day context, pit duration, and stint lengths).
2. Add one "single-variable-change" scenario pair to avoid mixing strategy and environment shifts in the same comparison.
3. Add a short assumptions block before results: "counterfactual scenario analysis, not causal identification."
4. Document a fallback policy: if Brier does not clearly improve over baseline, use calibrated ranking and uncertainty bounds for recommendation confidence.

*How we will address this critique by Wednesday:*

By Wednesday we will update Section 1 to lock the decision objective (points/top-10), rewrite Section 4 with controlled and fully specified scenarios, and revise Section 5 with an explicit confounding/observational-data caveat. We will also implement the scenario evaluation notebook to output calibrated probability deltas with bootstrap intervals and include a fallback decision rule when model quality is near baseline.

---