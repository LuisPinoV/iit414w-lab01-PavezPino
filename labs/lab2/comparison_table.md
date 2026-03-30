# Lab 1 vs Lab 2 — Comparison Table
## Luis Pino, Lucas Pavez
| Model / Baseline | Accuracy | Precision | Recall | F1 | ROC-AUC |
|------------------------|----------|-----------|--------|-------|---------|
| Majority class (Lab 1) | 0.71 | 0.00 | 0.00 | 0.00 | 0.50 |
| Domain heuristic (Lab 1)| 0.75 | 0.75 | 0.75 | 0.75 | 0.75 |
| Lab 2 model (LogReg/DT) | 0.837 | 0.843 | 0.829 | 0.836 | 0.910 |
## Primary metric: Grid 
## Interpretation (3–5 sentences)

The Lab 2 model improves over the majority baseline in all decision metrics and ROC-AUC, showing that the engineered features add useful signal.
Compared to the domain heuristic, gains are clear in the latest temporal holdout (Accuracy 0.837, F1 0.836, ROC-AUC 0.910).
Temporal validation is stricter than random CV, so these scores are a more realistic estimate for future races.
Error analysis indicates recurring misses in recovery drives and front-grid overconfidence, which motivates adding race-pace and circuit-context features in Lab 3.