# Leakage Audit — Hito 2

This notebook uses the locked temporal split and keeps the feature logic separated from the report logic.

## What is used in the model

- Pre-race context: `grid_position`, `driver_prior3_avg_finish`, `constructor_prior3_avg_finish`, `driver_circuit_prior_avg`, `circuit_type`, `constructor_tier`
- Scenario inputs: `n_stops`, `strategy_type`, `compound_sequence`, `stint1_length`, `stint2_length`, `stint3_length`, `avg_pit_stop_duration_s`, `total_pit_time_s`, `first_pit_lap`, `last_pit_lap`

These strategy inputs are treated as what-if controls. They are not interpreted as causal proof; they are only used to compare two counterfactual scenario rows under the same driver-race context.

## What is not used in the model fit

- Outcomes: `is_top10`, `is_top3`, `is_top5`, `finish_position`, `points`, `positions_gained`, `dnf`, `status`
- Audit / race-condition columns: `weather_actual`, `safety_car_periods`, `safety_car_laps`, `vsc_laps`, `wet_laps`, `avg_track_temp`, `avg_air_temp`
- Identifiers: `season`, `round`, `circuit`, `Driver`, `Team`

## Does the scenario-input treatment hold under both targets?

Yes. Both targets use the same split, the same feature set, and the same scenario scoring logic. The only thing that changes is the response variable (`is_top10` vs `is_top3`). That is why any disagreement between the targets is interpretable and worth reporting.

## Confounding limitation

Strategy choice is not random in historical F1 data. It is correlated with car pace, driver skill, weather, and incident likelihood. That means the model should be read as scenario-conditioned guidance, not as a causal effect estimate.
