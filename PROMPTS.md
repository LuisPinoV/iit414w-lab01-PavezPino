# PROMPTS.md __ AI Usage Log
# IIT414W - Lucas Pavez - Lab 0


## Entry [1] — [Looking for OS reproducibility checks] · [2026-03-10]

**Context:**

I was trying to get some example in the __setup_check.ipynb__ for reproducibility checks.

**Prompt(s):**
Prompt 1: "Give me some example of systems check for reproducibility, different from current executions. try with OS verifications."

prompt 2: "Please regrite some sections considering deprecate functions. "

**Relevant Output:**
The AI model give me a code section inplace and the chat give a summary of the code written.

```python
import platform
import shutil
import locale

print('── OS & Architecture Verification ──────────────────────────────')

# 1. Operating System and Release
print(f'OS Type        : {platform.system()}')
print(f'OS Release     : {platform.release()}')
print(f'OS Version     : {platform.version()}')

# 2. Python Executable Path
# Why: Ensures code runs in the intended environment (venv, conda, system Python)
print(f'\nPython Exec    : {sys.executable}')

# 3. Architecture (32-bit vs 64-bit)
# Why: Affects numeric precision, memory limits, and integer overflow behavior
print(f'Architecture   : {platform.architecture()[0]}')
print(f'Processor      : {platform.processor() or "Not determined"}')

# 4. Free Disk Space
# Why: Reproducibility fails if disk is full → no data caching, incomplete downloads
disk_usage = shutil.disk_usage('/')
print(f'\nFree Disk Space: {disk_usage.free / (1024**3):.1f} GB (on root partition)')
if disk_usage.free / (1024**3) < 5:
    print('⚠️  WARNING: Less than 5 GB free — FastF1 cache or data downloads may fail!')

# 5. Default Locale
# Why: Affects string sorting, number parsing, and CSV handling across regions
print(f'\nDefault Locale : {locale.getdefaultlocale()}')

print('\n🔍 REPRODUCIBILITY INSIGHT:')
print('   These OS checks ensure your code runs consistently:')
print('   • Same Python path → same package versions')
print('   • Same architecture → same float precision (32-bit vs 64-bit)')
print('   • Adequate disk space → data downloads complete without interruption')
print('   • Same locale → CSV parsing and sorting behave identically')
```

**Validation:**
- Test 1: Run the code to find syntax errors quickly
- Test 2: Analize the useful of information given.

**Adaptations:**
- The key parts of code like import libraries or execution of some lines with description of the system, but data like ``sys.executable` isn't useful to reproduce since every computer and OS have its own folder path.

**Final Decision:**
I keep partially because data like memory aviable is useful and relevant to find some posible errors and problems to execute the notebook.

## Entry [2] — [EDA Questions] 

**Context**

I needed exploratory data analysis questions for a Formula 1 dataset (2023–2024) obtained from the Jolpica F1 API.

**Prompt(s)**
Prompt 1: "give me examples for questions for exploratory data analysis using a dataset created from the Jolpica API"

**Relevant Output:**
The AI generated a list of exploratory data analysis questions and some tips on how to formulate good analytical questions.

**Validation:**

Checked that each question could be answered using the available dataset columns.

Verified that the questions were relevant to the dataset context.

**Adaptations:**

Removed questions that required data not present in the dataset.

**Final Decision:**
Used.


## Entry [3] — [Consistency Metric]

**Context**

I wanted to measure driver consistency using a metric better than standard deviation.

**Prompt(s):**
Prompt 1: "There is something better than standard deviation for measure a racer consistency?"

**Relevant Output:**
The AI suggested using the coefficient of variation and provided the formula and Python code to compute it per driver.

**Validation:**

Calculated the coefficient of variation manually for some drivers to verify the results.

**Adaptations:**

Filtered drivers with very few races to avoid unstable metrics.

**Final Decision:**
Rejected. It was not a good metric for the analysis.


## Entry [4] — [Interpretation of Results]

**Context**

I needed help interpreting some analysis results and getting advice about possible conclusions.

**Prompt(s)**
Prompt 1: "I want you to interpret this result, what does it mean? which conclusion can you give me?"

**Relevant Output**
The AI explained how extreme results or DNFs could affect variation metrics and suggested possible interpretations.

**Validation**

Compared the explanation with race outcomes in the dataset.

**Adaptations**

Added context about statistical limitations of the metric.

**Final Decision**
Partially used.


## Entry [5] — [Baseline experiment]

**Context**

I asked to copilot to make a metric for the simulate experiment, that we will improve in the lab 2.

**Prompt(s):**
Prompt 1: "Using fastf1 library Make A rule-based prediction that requires NO machine learning code.If grid > 14, predict DNF the race; otherwise predict "finish the race. apply it to every race in your validation set, and count how often it's right.

Compute accuracy = correct predictions / total predictions. Report this number clearly."

**Relevant Output:**
The Ai complete the code, for the asked task, with the context of the imported libraries.

the code given was:

```python
# ── Rule-Based Prediction: Grid Position → DNF Prediction ────────────────

# Get race data from 2023 season (validation set)
# Using races from mid-season onwards
season = 2023
races_to_fetch = [10, 11, 12, 13, 14, 15]  # Races 10-22

predictions = []
actual_results = []
race_info = []

print(f"Fetching {len(races_to_fetch)} races from {season} season...")
print("=" * 70)

for race_round in races_to_fetch:
    try:
        session = fastf1.get_session(season, race_round, 'R')  # 'R' for race
        session.load()
        
        race_name = session.event['EventName']
        
        # Get results dataframe with grid and DNF status
        results = session.results
        
        # Identify DNF drivers (Status is not 'Finished')
        dnf_drivers = set(results[results['Status'] != 'Finished'].index)
        finished_drivers = set(results[results['Status'] == 'Finished'].index)
        
        # Apply rule: grid > 14 → predict DNF, grid <= 14 → predict finish
        for driver_idx in results.index:
            grid_pos = results.loc[driver_idx, 'GridPosition']
            
            # Skip if no valid grid position (sometimes NaN for reserve/safety car)
            if pd.isna(grid_pos):
                continue
            
            grid_pos = int(grid_pos)
            
            # Apply rule
            predicted_dnf = grid_pos > 14
            actual_dnf = driver_idx in dnf_drivers
            
            is_correct = predicted_dnf == actual_dnf
            
            predictions.append({
                'race': race_name,
                'round': race_round,
                'driver': driver_idx,
                'grid_position': grid_pos,
                'predicted_dnf': predicted_dnf,
                'actual_dnf': actual_dnf,
                'correct': is_correct
            })
        
        print(f"✓ Round {race_round}: {race_name} - {len(results)} drivers")
        
    except Exception as e:
        print(f"✗ Round {race_round}: Error - {str(e)}")

# Convert to DataFrame for analysis
predictions_df = pd.DataFrame(predictions)

# Calculate metrics
total_predictions = len(predictions_df)
correct_predictions = predictions_df['correct'].sum()
accuracy = correct_predictions / total_predictions if total_predictions > 0 else 0

print("=" * 70)
print(f"\n📊 RULE-BASED PREDICTION RESULTS")
print(f"{'─' * 70}")
print(f"Rule: If GridPosition > 14 → Predict DNF | Otherwise → Predict Finish")
print(f"{'─' * 70}")
print(f"Total Predictions:  {total_predictions}")
print(f"Correct:            {correct_predictions}")
print(f"Incorrect:          {total_predictions - correct_predictions}")
print(f"\n🎯 ACCURACY: {accuracy:.4f} ({accuracy*100:.2f}%)")
print(f"{'─' * 70}")

# Breakdown by outcome
print(f"\nDetailed Breakdown:")
print(f"  - Correctly predicted DNF:    {predictions_df[(predictions_df['predicted_dnf'] == True) & (predictions_df['correct'] == True)].shape[0]}")
print(f"  - Correctly predicted Finish: {predictions_df[(predictions_df['predicted_dnf'] == False) & (predictions_df['correct'] == True)].shape[0]}")
print(f"  - False positives (DNF):      {predictions_df[(predictions_df['predicted_dnf'] == True) & (predictions_df['correct'] == False)].shape[0]}")
print(f"  - False negatives (Finish):   {predictions_df[(predictions_df['predicted_dnf'] == False) & (predictions_df['correct'] == False)].shape[0]}")

```

**Validation:**

Calculated the values  manually to verify the results.

**Adaptations:**

Filtered drivers with very few races to avoid unstable metrics.

**Final Decision:**

We keep the code.
