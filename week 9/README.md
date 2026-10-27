# House Sales Data Cleaning and Outlier Detection

A data cleaning and feature engineering notebook for the King County house sales dataset (`kc_house_data.csv`). It fixes invalid values, creates new features, detects price outliers with the IQR method, and exports a cleaned CSV.

## Notebook

`WEEEK_9_modified.ipynb`

## Dataset

- **File:** `kc_house_data.csv`
- **Columns used:** `price`, `bedrooms`, `bathrooms`, `sqft_living`, `sqft_lot`, `date`, `yr_built`, `yr_renovated`
- The notebook reads the file from Google Drive in Colab. Update the path in the first cell if you run it elsewhere.

## Requirements

- Python 3
- pandas
- numpy
- matplotlib
- seaborn

```bash
pip install pandas numpy matplotlib seaborn
```

## What the Notebook Does

1. **Load and inspect:** reads the CSV, strips whitespace from column names, and prints missing values per column.
2. **Remove duplicates:** drops duplicate rows.
3. **Fix room counts:** converts `bedrooms` and `bathrooms` to numeric, treats values of 0 or less as missing, and fills them with the column median.
4. **Parse dates:** converts `date` to datetime and `yr_built` / `yr_renovated` to numeric.
5. **Age features:**
   - `age_at_sale`: year sold minus year built
   - `years_since_reno`: year sold minus year renovated (only for renovated houses)
   - Negative values are set to missing.
6. **Price per square foot:**
   - `price_per_living_sqft`: price divided by living area
   - `price_per_lot_sqft`: price divided by lot size
7. **Outlier detection (IQR):** computes Q1, Q3 and IQR of `price`, with bounds at Q1 − 1.5 × IQR and Q3 + 1.5 × IQR. Prices outside the bounds are flagged in `price_outlier`.
8. **Visualization:** draws a box plot of house prices and prints the outlier records.
9. **Export:** saves the cleaned data to `house_sales_cleaned.csv`.

## New Columns

| Column | Description |
|---|---|
| `year_sold` | Year extracted from the sale date |
| `age_at_sale` | Age of the house when sold |
| `years_since_reno` | Years between renovation and sale |
| `price_per_living_sqft` | Price per sqft of living area |
| `price_per_lot_sqft` | Price per sqft of lot |
| `price_outlier` | `True` if the price is an IQR outlier |

## How to Run

1. Upload `kc_house_data.csv` to Google Drive (or keep it locally).
2. Open `WEEEK_9_modified.ipynb` in Google Colab or Jupyter.
3. Update the file path in the first cell if needed.
4. Run all cells.

## Output

- Printed missing-value counts, IQR bounds and outlier count
- Box plot of house prices
- Table of outlier records
- `house_sales_cleaned.csv`: the cleaned dataset
