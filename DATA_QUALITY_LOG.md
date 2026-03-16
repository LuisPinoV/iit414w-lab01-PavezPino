# Data Quality Log
# Jolpica API Dataset

## Issue 1: grid — Pit Lane Starts

**What:** Drivers starting from the pit lane appear with grid = 0.

**Classification:** special value.

**Impact:** Since the value is 0, it breaks the scale of the positions and affects the mathematical calculations  

**Decision:** Keep

**Justification:** These are legitimate race events and must remain in the dataset.

## Issue 2: laps — Outliers Due to Early Retirements

**What:** Some drivers complete very few laps (0–5 laps) due to crashes or mechanical failures.

**Classification:** Outlier.

**Impact:** These  values increase variance in lap-based analyses.

**Decision:** Keep all observations but note them when interpreting distributions.

**Justification:** Early retirements are genuine race outcomes and represent important events in the dataset.

## Issue 3: date — String Format

What: The race date is returned as a string in ISO format rather than a datetime object.

Classification: Data type issue.

Impact: Cannot be performed correctly without conversion.

Decision: Convert the column to datetime.

Justification: Correct temporal data types are necessary for time-series and seasonal analyses.

## Issue 4: Race Results Pagination

What: The API returns results in paginated responses.

Classification: Data retrieval completeness issue.

Impact: If pagination is not handled correctly, some race results may be missing from the dataset.

Decision: Implement a loop that retrieves all pages until the total number of records is reached.

Justification: Ensures the dataset includes all available race results.