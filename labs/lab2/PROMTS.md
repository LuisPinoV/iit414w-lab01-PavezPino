# PROMPTS.md __ AI Usage Log
# IIT414W - Lucas Pavez - Lab 2


## Entry [1] — [Looking for OS reproducibility checks] · [2026-03-21]

**Context:**
I asked to claude code to construct an api request, that make an api call to jolpica, then unify the data of many races and then i used to get the data for lab 1 (fixed) and lab 2.

**Prompt(s):**

"Import request and download a few races temporal splited, 2022, 2023 and list the features, make a dataframe in pandas that unify the data, considering qualifying races"
**Relevant Output:**

The code developed also add an DNF consideration but the code in general terms was:

```python
# ── Data Loading: F1 Results + Qualifying via Ergast API ─────────────────────
import requests
import pandas as pd

# ── Config ────────────────────────────────────────────────────────────────────
SEASONS   = [2022, 2023, 2024]
FEATURES  = ['grid', 'driver', 'q3_time_sec']
TARGET    = 'top10'
BASE_URL  = "https://api.jolpi.ca/ergast/f1"

# ── Helpers ───────────────────────────────────────────────────────────────────
def _paginate(url_template: str, table_key: str, row_key: str) -> list:
    """Generic paginator for any Ergast endpoint."""
    offset, limit, rows = 0, 100, []
    while True:
        url  = f"{url_template}?limit={limit}&offset={offset}"
        data = requests.get(url, timeout=30).raise_for_status() or \
               requests.get(url, timeout=30).json()['MRData']
        # cleaner version:
        resp = requests.get(url, timeout=30)
        resp.raise_for_status()
        data  = resp.json()['MRData']
        total = int(data['total'])
        rows.extend(data[table_key][row_key])
        offset += limit
        if offset >= total:
            break
    return rows

def time_to_sec(t: str) -> float | None:
    """Convert 'm:ss.mmm' string to total seconds."""
    if not t:
        return None
    try:
        m, s = t.split(':')
        return int(m) * 60 + float(s)
    except Exception:
        return None

# ── Loaders ───────────────────────────────────────────────────────────────────
def get_season_results(year: int) -> pd.DataFrame:
    rows = []
    for race in _paginate(f"{BASE_URL}/{year}/results.json", 'RaceTable', 'Races'):
        for r in race['Results']:
            rows.append({
                'season'    : int(race['season']),
                'round'     : int(race['round']),
                'race_name' : race['raceName'],
                'circuit'   : race['Circuit']['circuitId'],
                'date'      : race['date'],
                'driver'    : r['Driver']['driverId'],
                'driver_name': f"{r['Driver']['givenName']} {r['Driver']['familyName']}",
                'constructor': r['Constructor']['constructorId'],
                'grid'      : int(r['grid']),
                'position'  : int(r['position']) if r['position'].isdigit() else None,
                'points'    : float(r['points']),
                'status'    : r['status'],
                'laps'      : int(r['laps']),
            })
    return pd.DataFrame(rows)


def get_qualifying_results(year: int) -> pd.DataFrame:
    rows = []
    for race in _paginate(f"{BASE_URL}/{year}/qualifying.json", 'RaceTable', 'Races'):
        for r in race['QualifyingResults']:
            rows.append({
                'season'     : int(race['season']),
                'round'      : int(race['round']),
                'driver'     : r['Driver']['driverId'],
                'quali_pos'  : int(r['position']),
                'q1_time_sec': time_to_sec(r.get('Q1')),
                'q2_time_sec': time_to_sec(r.get('Q2')),
                'q3_time_sec': time_to_sec(r.get('Q3')),   # None si eliminado antes
            })
    return pd.DataFrame(rows)

# ── Load & merge ──────────────────────────────────────────────────────────────
print("Loading F1 data...")

results, qualis = [], []
for year in SEASONS:
    df_r = get_season_results(year)
    df_q = get_qualifying_results(year)
    print(f"  {year}: {len(df_r)} results | {len(df_q)} quali entries")
    results.append(df_r)
    qualis.append(df_q)

df_results = pd.concat(results, ignore_index=True)
df_quali   = pd.concat(qualis,  ignore_index=True)

df_all = df_results.merge(
    df_quali[['season', 'round', 'driver',
              'quali_pos', 'q1_time_sec', 'q2_time_sec', 'q3_time_sec']],
    on=['season', 'round', 'driver'],
    how='left'
)

# ── Feature engineering ───────────────────────────────────────────────────────
df_all['date']  = pd.to_datetime(df_all['date'])
df_all[TARGET]  = (df_all['position'] <= 10)

# Encode driver como categoría ordinal (modelo puede usarlo directo)
df_all['driver_enc'] = df_all['driver'].astype('category').cat.codes

# ── Dataset listo para modelo ─────────────────────────────────────────────────
# Nota: q3_time_sec es NaN para pilotos eliminados en Q1/Q2 → tratar antes de modelar
FEATURE_COLS = {
    'grid'       : 'grid',          # posición de largada
    'driver'     : 'driver_enc',    # piloto codificado
    'q3_time_sec': 'q3_time_sec',   # tiempo Q3 en segundos (NaN si no clasificó Q3)
}

X = df_all[list(FEATURE_COLS.values())]
y = df_all[TARGET]

print(f"\nDataset final: {len(df_all)} filas | {X.isna().sum().to_dict()} NaNs por feature")
print(df_all[['race_name', 'driver', 'grid', 'q3_time_sec', 'driver_enc', TARGET]].head(10))
```

**Adaptations:**
We delete the DNF consideration because the only task was get the top 10.

**Final Decision:**
The code was almost keep at all but with a few changes.