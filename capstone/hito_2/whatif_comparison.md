# What-If Comparison — Hito 2

This is the required side-by-side scenario comparison that makes the two targets disagree on the recommendation.

## Fixed context

The base row is a 2023 Bahrain Grand Prix row for `VER` with the following context:

- Season: 2023
- Circuit: Bahrain Grand Prix
- Circuit type: permanent
- Driver: VER
- Team: Red Bull
- Grid position: 1
- Constructor tier: front
- Driver prior-3 average finish: 2.67
- Constructor prior-3 average finish: 3.50
- Driver circuit prior average: 9.20

## Scenario pair

| Scenario | n_stops | compound_sequence | stint_lengths | avg pit duration | `P(is_top10)` | `P(is_top3)` |
|---|---:|---|---|---:|---:|---:|
| A: 1-stop S-M | 1 | `S-M` | 40 | 22.0 s | 0.9547 | 0.9059 |
| B: 2-stop S-M-S | 2 | `S-M-S` | 22-20 | 22.0 s | 0.9677 | 0.8354 |

## Interpretation

- `is_top10` prefers Scenario B because it gives the larger probability of finishing in the points.
- `is_top3` prefers Scenario A because the extra stop reduces the modeled podium probability.
- The disagreement is the point of Hito 2: a top-10-only view would recommend the two-stop plan, but the expansion target surfaces the podium trade-off.

## Delta

- Delta `P(is_top10)` = A - B = -0.0130
- Delta `P(is_top3)` = A - B = +0.0705

## Decision takeaway

If the team cares primarily about scoring points, Scenario B is the better recommendation. If the team cares about podium upside or protecting the top-3 window, Scenario A is the better recommendation. The two-target setup makes that trade-off visible.
