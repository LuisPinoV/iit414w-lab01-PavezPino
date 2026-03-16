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


Entry [4] — [Interpretation of Results]

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