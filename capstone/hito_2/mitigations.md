# Mitigations — Hito 2

This file records the main risks in the midpoint model and what we would change before deployment.

## Risks

1. **Strategy confounding**  
   Historical strategy choices are correlated with hidden factors such as car pace, weather, driver quality, and race incidents. The model can learn those correlations instead of true strategy effect.

2. **Small-slice instability**  
   Some slices, especially `no_stop` and `front` / `is_top3`, are small or highly imbalanced. Metrics there can look better or worse than they really are.

3. **Probability calibration drift**  
   Even with calibration on 2022, the 2023–2024 distribution may not match perfectly. Brier score can move if the race regime changes.

4. **Over-reading the what-if output**  
   The scenario comparison is descriptive and counterfactual, not causal. A user could easily read it as a guaranteed strategy effect if the report is not careful.

5. **Feature completeness**  
   The notebook does not model every useful race-weekend signal. If a pre-race weather forecast or more reliable tire-degradation proxy becomes available, the model should be retrained with it.

## Mitigations before deployment

- Keep the scenario language explicitly conditional: "under our training distribution" rather than "causes".
- Report slice sizes with every error-analysis table and avoid strong claims on tiny groups.
- Use bootstrap confidence intervals for the scenario deltas in the final report.
- Re-run calibration each season and compare Brier / log loss before trusting the model operationally.
- If `is_top3` remains unstable, use it as a tie-breaker rather than a hard decision rule.
