# Technical Memo (Non-Technical Audience)

**To:** Head of Strategy, Formula 1 Team  
**From:** Data Analysis Team  
**Subject:** Pre-race prediction of driver points contribution

## Executive Summary
We evaluated five prediction approaches to estimate how many points each driver is likely to score in a race using only pre-race information. Our best approach was a Random Forest model with an average test error of **2.760 points** in the 2024 season. For context, a simple grid-based rule reached **3.092**, and linear models were around **3.38-3.40**.

## What this means in practice
On average, the best model misses by about 2.8 points per driver-race prediction. This is accurate enough to support scenario planning (for example, expected points under conservative vs aggressive strategy) but not precise enough to treat as a certainty for a single race.

## Why this model is preferred
The Random Forest model performed best because race outcomes are not purely linear. The value of starting near the front and recent performance trends interact in complex ways. The model can capture those interactions better than linear alternatives.

## Risk and confidence
The Random Forest model had a stronger fit on training seasons than on 2024 test data (train MAE **1.789** vs test MAE **2.760**). This gap indicates some overfitting risk: the model learns historical patterns well, but part of that advantage may not transfer perfectly to future races.

## Key limitation
The model does not include race-day disruptions such as weather shifts, incidents, penalties, or reliability failures. These factors can dominate individual race results and are a source of unavoidable uncertainty.

## Recommendation
Use the Random Forest model as the main planning tool for expected points, but always compare it with the grid heuristic baseline before strategy meetings. If both agree, confidence is higher. If they diverge, treat the race as high-uncertainty and stress-test decisions with multiple scenarios.
