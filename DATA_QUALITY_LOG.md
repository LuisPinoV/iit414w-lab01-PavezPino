# Data Quality Log
# Jolpica API Dataset

## Issue 1: grid — Pit Lane Starts

Some drivers have grid = 0, indicating a start from the pit lane rather than a standard grid position.

**Classification:** MAR

Impact: Grid position statistics may be distorted because 0 does not represent a valid grid slot.

Decision: Keep the value but flag grid = 0 as a pit lane start.

Justification: This preserves the race event while allowing analyses to treat pit lane starts separately if needed.

Issue 3: status — High Cardinality Text Categories

What: The status column contains many textual categories (e.g., “Finished”, “Accident”, “Engine”, “Collision”).

Classification: Data consistency / categorical normalization issue.

Impact: Difficult to group or analyze retirement causes consistently.

Decision: Create a derived variable (finished vs not finished) for analysis.

Justification: Simplifying categories improves interpretability while preserving the original detailed information.

Issue 4: laps — Outliers Due to Early Retirements

What: Some drivers complete very few laps (e.g., 0–5 laps) due to crashes or mechanical failures.

Classification: Outlier (but valid observation).

Impact: These extreme values increase variance in lap-based analyses.

Decision: Keep all observations but note them when interpreting distributions.

Justification: Early retirements are genuine race outcomes and represent important events in the dataset.

Issue 5: race_name / circuit — Naming Variations

What: Race names and circuit identifiers may vary slightly between seasons (e.g., sponsor changes or official naming updates).

Classification: Data consistency issue.

Impact: Grouping races across seasons may produce duplicate categories.

Decision: Use circuit identifiers as the primary grouping variable.

Justification: Circuit IDs are stable identifiers and avoid inconsistencies caused by naming changes.

FastF1 Dataset
Issue 6: LapTime — Missing Lap Times

What: Some laps have missing LapTime values, particularly for out laps, in laps, or invalidated laps.

Classification: MNAR.

Impact: Missing lap times prevent accurate calculation of pace or lap-time statistics.

Decision: Exclude laps with missing LapTime from pace-related analyses.

Justification: These laps do not represent competitive laps and would distort performance metrics.

Issue 7: SectorTimes — Incomplete Sector Data

What: Sector times (Sector1Time, Sector2Time, Sector3Time) can be missing when laps are aborted or invalidated.

Classification: MNAR.

Impact: Prevents precise sector performance comparisons between drivers.

Decision: Use only laps where all sector times are present for sector analysis.

Justification: Partial sector data would bias comparisons between drivers.

Issue 8: Telemetry (Speed, Throttle, Brake) — High Frequency Noise

What: Telemetry data contains high-frequency fluctuations and noise due to sensor sampling.

Classification: Measurement noise.

Impact: Raw telemetry may exaggerate short spikes and distort averages.

Decision: Apply smoothing or aggregation (e.g., mean speed over segments).

Justification: Smoothing reduces noise and highlights meaningful driving patterns.

Issue 9: Driver Identifiers — Abbreviations vs Full Names

What: FastF1 commonly uses driver abbreviations (e.g., VER, HAM) instead of full names.

Classification: Data representation inconsistency.

Impact: Difficult to merge with other datasets that use full driver names.

Decision: Create a mapping table to convert abbreviations to full names.

Justification: Standardized identifiers enable merging across datasets.

Issue 10: Session Coverage — Missing Sessions

What: Some sessions may fail to load due to API rate limits, caching issues, or missing telemetry data.

Classification: Data availability issue.

Impact: Analyses may miss entire sessions or races.

Decision: Verify session loading and retry when necessary.

Justification: Ensures dataset completeness and avoids biased results.

If you want, I can also help you improve these so they look more “academic” (professors often expect specific wording for MCAR/MAR/MNAR explanations).