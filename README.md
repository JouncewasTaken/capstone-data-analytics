# capstone-data-analytics

Final capstone project for my data analytics course. It uses gradient boosted decision trees (LightGBM and CatBoost, with logistic regression as a baseline) to estimate 30-day hospital readmissions from Medicare claims plus social determinants of health (SDOH) at the ZIP code level. SHAP values explain the LightGBM model, and a Power BI report presents the results.

## Results

Held-out test set (20% of inpatient stays):

| Model | ROC AUC | Brier score | Accuracy |
|---|---|---|---|
| Logistic Regression | 0.785 | 0.082 | 0.898 |
| LightGBM | 0.843 | 0.070 | 0.915 |
| CatBoost | 0.843 | 0.071 | 0.912 |

About 10.5% of test stays were readmissions, so accuracy alone isn't very telling. ROC AUC and Brier score are the better comparison.

## What's here

| File | What |
|---|---|
| `30DRadmit.ipynb` | Notebook: load and join the data, build features, train the models, SHAP, write the report CSVs |
| `Sources.zip` | Source data (see Data below) |
| `exports/` | CSVs the notebook writes; the Power BI report reads these |
| `30dReadmitModelPerf.pbix` | Power BI report |

## How to run

The notebook is set up for Google Colab. A copy is also here: https://colab.research.google.com/drive/1XVYhqUvSbBiSOtGDFlssSFBAopQKZ954?usp=sharing

1. Put `Sources.zip` in your Google Drive (`MyDrive`).
2. In Colab, mount Drive and unzip the CSVs straight into `MyDrive` (the zip has a nested `Sources/Sources/` folder, `-j` flattens it):

   ```
   from google.colab import drive
   drive.mount('/content/drive')
   !unzip -j -o /content/drive/MyDrive/Sources.zip 'Sources/Sources/*.csv' -d /content/drive/MyDrive
   ```

   To keep the files somewhere else, change `data_dir` in the first cell.
3. Run all cells. The notebook installs CatBoost and SHAP itself. The output CSVs go to `MyDrive/exports`.

The unzipped data is about 1.2 GB, and a full run takes a while.

## Data

- **CMS synthetic Medicare claims** (beneficiary files 2020-2023 and inpatient, outpatient, SNF, hospice, HHA, carrier and DME claims). Synthetic data, no real patients.
- **CMS Care Compare**: Hospital General Information and the Hospital Readmissions Reduction Program measures (expected readmission rates by condition).
- **CDC PLACES** (ZIP level): grouped into chronic conditions, poor physical health and preventive care.
- **PolicyMap** (ZIP level): education levels, poverty rate, renter rate, uninsured rate.

An AHRQ HCUP file (https://hcup-us.ahrq.gov/nisoverview.jsp) isn't included because of HCUP's data sharing limits. The notebook doesn't read it, so everything runs without it.

## Power BI report

`30dReadmitModelPerf.pbix` is linked straight to the CSVs in `exports/`, not to cloud storage. After a fresh run, point its data source at your own `exports` folder and refresh.
