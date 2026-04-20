# Framing Decision

## Business Question
How many constructor championship points should we expect each driver to contribute in this Sunday race, using only information available before the race starts? This helps a team principal plan strategy and risk for both cars.

## Target Variable
`points` per driver in a race (continuous value from 0 upward).

## Metric
The primary metric is **MAE (Mean Absolute Error)**. MAE is appropriate because the target is continuous and the business cost is naturally measured in point differences: missing by 1 point is less harmful than missing by 8 points. MAE is also easy to explain to non-technical stakeholders as the average absolute points error.

## Rejected Alternative
I considered **binary classification** (`top10` yes/no) with F1-score, but rejected it because it collapses very different outcomes into the same class. For strategy planning, predicting P1 (25 points) and P10 (1 point) as the same "positive" outcome removes critical value information. I also considered multiclass bands (`no_points/scoring/podium`), but class boundaries are arbitrary and lose granularity needed for constructor points planning.