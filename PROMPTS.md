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

