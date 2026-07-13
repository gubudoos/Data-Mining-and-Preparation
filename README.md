Pranav
Brandon
Jacqueline
Taeyoon
Naji


# Data Preparation Group Project — README

## Overview

This Jupyter notebook performs a full data preparation and quality analysis pipeline on the **Chicago Food Inspections** dataset. It covers:

1. **Single-Column Profiling** — statistical and visual profiling of every column 
2. **Data Cleaning Pipeline** — multi-step cleaning (violations, risk, address, geo, facility type NLP)
3. **Association Rule Mining (ARM)** — discovers frequent attribute co-occurrence patterns
4. **Functional Dependency (FD) Discovery** — finds minimal non-trivial FDs
5. **Inclusion Dependency (IND) Analysis** — measures value-set overlap between columns
6. **Data Quality Pipeline** — ARM-based anomaly detection + FD-based violation repair

---

## Prerequisites

### Python Version
Python 3.9 or above is recommended.

### Required Libraries

Install all dependencies before running the notebook:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn tqdm mlxtend sentence-transformers thefuzz
```

| Library | Purpose |
|---|---|
| `pandas` | Data loading and manipulation |
| `numpy` | Numerical operations |
| `matplotlib` | Plotting and visualisations |
| `seaborn` | Advanced visualisations (heatmaps) |
| `scikit-learn` | Cosine similarity for NLP matching |
| `tqdm` | Progress bars |
| `mlxtend` | Apriori algorithm and association rules |
| `sentence-transformers` | NLP model for Facility Type normalisation |
| `thefuzz` | Fuzzy string matching for City cleaning |

> **Note:** `sentence-transformers` will automatically download the `all-MiniLM-L6-v2` model (~80 MB) on first run. An internet connection is required for this step.

---

## Input Data

Place the following CSV file in the **same directory** as the notebook before running:

```
Food_Inspections_20240215.csv
```

This is the Chicago Food Inspections dataset. It must contain at minimum these columns:

- `Inspection ID`, `DBA Name`, `AKA Name`, `License #`
- `Facility Type`, `Risk`, `Address`, `City`, `State`, `Zip`
- `Inspection Date`, `Inspection Type`, `Results`
- `Violations`, `Latitude`, `Longitude`, `Location`

---

## How to Run

### Step 1 — Launch Jupyter

```bash
jupyter notebook Data_Preparation_Group_Project_Main.ipynb
```

Or with JupyterLab:

```bash
jupyter lab Data_Preparation_Group_Project_Main.ipynb
```

### Step 2 — Run All Cells in Order

Use **Kernel → Restart & Run All** to execute the entire notebook from top to bottom. The cells must be run in sequence because later cells depend on variables set in earlier ones.

### Step 3 — Key Execution Order

The notebook is structured as follows:

| Cell Section | What it does |
|---|---|
| **Import Libraries** | Loads all dependencies; prints ✅ if successful |
| **Single-Column Profiling** | Defines `run_single_column_profiling()` |
| **Constants & Helpers** | Sets all tunable parameters and I/O utilities |
| **Cleaning Steps 0–4** | Defines cleaning functions for each data dimension |
| **Master Pipeline (`clean()`)** | Chains all cleaning steps; writes `_cleaned.csv` |
| **Association Rule Mining** | Defines `run_association_rule_mining()` |
| **Functional Dependencies** | Defines `run_functional_dependencies()` |
| **Inclusion Dependencies** | Defines `run_inclusion_dependencies()` |
| **Perform Tasks** | Actually calls all the above functions |
| **Handle Data Quality Problem** | ARM anomaly detection + FD repair pipeline |

---

## Outputs

| Output File | Description |
|---|---|
| `Food_Inspections_20240215_cleaned.csv` | Cleaned dataset after the full pipeline |
| `minimal_FDs.csv` | All discovered minimal functional dependencies |
| `arm_anomaly_detection.png` | ARM anomaly visualisation chart |
| `ind_full_analysis.png` | IND coverage heatmap and charts |
| `Food_Inspections_cleaned.csv` | Final output after data quality repair |




## Tunable Parameters

These constants at the top of the **Constants** cell can be adjusted:

| Parameter | Default | Description |
|---|---|---|
| `CITY_FUZZ_THRESHOLD` | `80` | Minimum fuzzy score to remap a city name to CHICAGO |
| `DROP_NON_TARGET_CITIES` | `True` | Whether to drop non-Chicago rows |
| `TARGET_ZIP_PREFIXES` | `("606", "607", "608")` | Valid Chicago ZIP prefixes |
| `min_support` (ARM) | `0.05` | Minimum support for Apriori |
| `arm_conf_threshold` | `0.80` | Minimum confidence for anomaly detection |
| `arm_lift_threshold` | `1.5` | Minimum lift for anomaly detection |
| `fd_strategy` | `"majority_vote"` | FD repair strategy (`majority_vote` or `flag_only`) |

---

## Quick Reference — Calling Functions Individually

```python
# Profiling only
run_single_column_profiling("Food_Inspections_20240215.csv")

# Cleaning only
clean_file = clean("Food_Inspections_20240215.csv")

# ARM only (on cleaned file)
arm_rules = run_association_rule_mining(clean_file)

# FD only (on cleaned file, dropping metadata columns)
fds_df = run_functional_dependencies(clean_file, drop_cols=["Inspection ID", "Violations", ...])

# IND only
ind_results, type_df = run_inclusion_dependencies(clean_file, min_coverage=0.0)

# Full quality pipeline (pass pre-computed arm_rules and fds_df to save time)
df_clean, report = run_data_quality_pipeline(
    clean_file,
    arm_rules=arm_rules,
    fds_df=fds_df,
)
```
