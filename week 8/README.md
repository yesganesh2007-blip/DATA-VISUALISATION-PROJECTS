# Week 8 - Student Performance Visualisation

A Jupyter notebook that visualises how exam scores relate to parental education, lunch type and test preparation, and ends with a short educational equity report.

## Dataset
- **File:** `Studentperformance_preprocessed.csv` (output of the Week 7 cleaning notebook)
- **Rows:** 1,000 students
- **Columns used:** gender, race/ethnicity, parental level of education, lunch, test preparation course, math score, reading score, writing score, Total Score, Percentage

## What the notebook does
1. **Grouped bar charts** - average Math, Reading and Writing scores by parental level of education and by lunch type.
2. **Box plots** - score spread for students who completed the test preparation course vs. those who did not, with a mean / standard deviation / variance table.
3. **Scatter plots and correlation heatmap** - relationships between Math, Reading and Writing scores.
4. **Educational equity and policy recommendations** - key findings, recommendations and limitations.

## Requirements
- Python 3
- pandas
- numpy
- matplotlib
- seaborn

## How to run
1. Upload `Studentperformance_preprocessed.csv` to Google Drive in `My Drive/Python Programs/`.
2. Open `week8.ipynb` in Google Colab.
3. Run all cells from top to bottom (the first cell mounts Google Drive).

To run it outside Colab, remove the `google.colab` import and the `drive.mount` line, then change the path in the `pd.read_csv` line to the local location of the CSV.

## Note on loading the data
The test preparation answer `None` (did not take the course) is read by pandas as a missing value by default. The notebook uses `keep_default_na=False` so those 642 students are kept. It also fixes `Bachelor'S Degree` to `Bachelor's Degree`, which was caused by `.str.title()` in Week 7.

## Main findings
| Factor | Finding |
|---|---|
| Lunch type | Standard lunch students average 70.8% vs 62.2% for free/reduced; the biggest gap is in Math (about 11 points). |
| Parental education | Scores rise with parental education; the gap is largest in Writing (about 13 points) and smallest in Math (about 8 points). |
| Test preparation | Students who completed it score about 7.6 points higher and are less spread out; 16.8% score below 60% vs 35.0% without it. |
| Subject relationships | Reading and Writing correlate at 0.95; Math correlates with each at about 0.80 to 0.82. |

## Limitations
- The data shows relationships, not proof of cause.
- Lunch type and parental education only roughly represent a family's situation.
- The dataset is a public sample of 1,000 students, so results should be checked against real school data before making decisions.

## Files
- `week8.ipynb` - the notebook
- `Studentperformance_preprocessed.csv` - input data from Week 7
- `README.md` - this file
