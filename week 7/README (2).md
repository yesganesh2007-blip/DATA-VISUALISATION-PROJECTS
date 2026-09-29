# Student Performance Analysis

A Jupyter notebook that cleans a student exam-scores dataset, summarises the subject scores, builds total-marks and percentage features, and flags extreme performance outliers.

## What it does

| Step | Description |
|------|-------------|
| 1. Load & inspect | Reads `StudentsPerformance.csv`, shows shape, dtypes, missing values and duplicates. Works both in Google Colab (mounts Drive) and locally. |
| 2. Clean categorical features | Standardises `gender`, `race_ethnicity`, `parental_education`, `lunch` and `test_prep_course`. |
| 3. Validate scores | Ensures scores are numeric and within 0-100, then drops rows with missing/invalid values and exact duplicates. |
| 4. Descriptive statistics | Mean, median, mode, standard deviation, min/max, quartiles (Q1, Q2, Q3), IQR and skewness for each subject, plus histograms. |
| 5. Calculated features | `total_marks`, `percentage` and `performance_band`. |
| 6. Outlier detection | Flags outliers per subject and for total marks using the IQR rule, and marks *extreme* outliers using an additional z-score check. |
| 7. Export | Saves the cleaned data, statistics, outlier tables and charts to an `output/` folder. |

## Dataset

The notebook expects a CSV named `StudentsPerformance.csv` with 1,000 rows and these 8 columns:

| Original column | Cleaned name | Type | Expected values |
|---|---|---|---|
| gender | `gender` | category | Female, Male |
| race/ethnicity | `race_ethnicity` | category | Group A - Group E |
| parental level of education | `parental_education` | ordered category | Some High School < High School < Some College < Associate's Degree < Bachelor's Degree < Master's Degree |
| lunch | `lunch` | category | Standard, Free/Reduced |
| test preparation course | `test_prep_course` | category | None, Completed |
| math score | `math_score` | number | 0-100 |
| reading score | `reading_score` | number | 0-100 |
| writing score | `writing_score` | number | 0-100 |

## Requirements

- Python 3.9+
- `pandas`, `numpy`, `matplotlib`, `seaborn`
- Jupyter (or Google Colab)

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

## How to run

**Locally**

1. Put `Student.ipynb` and `StudentsPerformance.csv` in the same folder (the CSV may also be in a `data/` subfolder).
2. Run `jupyter notebook Student.ipynb` and choose **Run All**.

**Google Colab**

1. Upload `Student.ipynb` to Colab and place `StudentsPerformance.csv` in your Drive's *My Drive* folder (or upload it to the Colab session).
2. Run all cells and approve the Drive-mount prompt when it appears.

## Methods

**Categorical cleaning.** Values are trimmed, whitespace is collapsed, and case and apostrophe style are ignored (`"Bachelors degree"`, `"bachelor’s degree"` and `"BACHELOR'S DEGREE"` all become `Bachelor's Degree`). Each value is mapped to a canonical label; anything unrecognised is printed as a warning and treated as missing. A before/after listing of the categories is shown for every column.

**Score validation.** Scores are converted to numbers; anything non-numeric or outside 0-`MAX_SCORE` becomes missing. Rows with missing values and exact duplicate rows are removed and the counts are reported. The DataFrame index still matches the row number in the original CSV.

**Statistics.** `std` is the sample standard deviation (n - 1). `median (Q2)` is the 50th percentile, `Q1`/`Q3` the 25th/75th percentiles, and `IQR = Q3 - Q1`. If several modes exist, the smallest is shown.

**Calculated features.**

- `total_marks = math + reading + writing`
- `percentage = total_marks / (MAX_SCORE x number of subjects) x 100` (i.e. `/ 300 x 100`), rounded to 2 decimals
- `performance_band`: Below 40, 40-49, 50-59, 60-74, 75-89, 90-100 (lower edge inclusive)

**Outlier detection.** Applied to each subject and to `total_marks`:

- *Outlier:* value below `Q1 - 1.5 x IQR` or above `Q3 + 1.5 x IQR` (Tukey fences).
- *Extreme outlier:* an outlier whose absolute z-score is at least 3.
- Each student also gets `n_outlier_subjects`, `outlier_subjects`, `outlier_direction` (low / high / mixed / none) and `extreme_subjects`.
- Outliers are **flagged, not removed**, so you can decide how to treat them.

Note that `percentage` is a linear rescaling of `total_marks`, so it would flag exactly the same students; it is therefore not tested separately.

## Outputs

Everything is written to `output/`:

| File | Contents |
|---|---|
| `students_cleaned.csv` | Cleaned dataset with all calculated and outlier-flag columns (`row_id` = row in the original CSV) |
| `score_statistics.csv` | Descriptive statistics per subject |
| `outlier_summary.csv` | Fences and outlier counts per subject and for total marks |
| `outlier_report.csv` | One row per flagged student, most severe first |
| `score_distributions.png` | Histograms with mean and median |
| `outlier_boxplots.png` | Box plots for subjects and total marks |

## Configuration

Edit the settings at the top of the notebook:

| Setting | Default | Meaning |
|---|---|---|
| `DATA_FILE` | `"StudentsPerformance.csv"` | Input file name |
| `OUTPUT_DIR` | `output` | Folder for results |
| `MAX_SCORE` | `100` | Maximum marks per subject |
| `IQR_MULTIPLIER` | `1.5` | Fence width (use `3.0` for a stricter, "far out" rule) |
| `Z_THRESHOLD` | `3.0` | |z-score| required for an outlier to count as extreme |

## Sanity check against the original run

These are the values printed by the original notebook on the full 1,000-row dataset; the new notebook's statistics table should match them.

| | Math | Reading | Writing |
|---|---|---|---|
| Mean | 66.09 | 69.17 | 68.05 |
| Median | 66 | 70 | 69 |
| Std | 15.16 | 14.60 | 15.20 |
| Q1 / Q3 | 57 / 77 | 59 / 79 | 57.75 / 79 |
| Min / Max | 0 / 100 | 17 / 100 | 10 / 100 |

From these quartiles the lower IQR fences work out to about 27 (math), 29 (reading) and 25.9 (writing). The upper fences (about 107-111) lie above the maximum possible score of 100, so on this dataset only **unusually low** scores can be flagged as outliers.

## Limitations

- Outlier rules assume scores are roughly unimodal; a flagged student is unusual relative to the whole cohort, not necessarily an error.
- Scores are capped at 100, so the IQR rule cannot detect unusually *high* performance when the upper fence exceeds the cap.
- The dataset contains no student identifier, so students are referenced by their row number.
