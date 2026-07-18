# Mini Project: Classification Algorithms Exploration
### Algorithm: Gradient Boosting / XGBoost

## Team

- Raghad Alharbi
- Maram Alzahrani
- Turki
- Mohammed

## Overview

This project explores **Gradient Boosting / XGBoost** applied to a real-world binary classification
problem: predicting whether a patient has **cardiovascular disease** (`cardio` = 0 or 1) from routine
clinical and lifestyle measurements (age, blood pressure, cholesterol, glucose, and habits such as
smoking, alcohol use, and physical activity).

As a bonus, we benchmark XGBoost against a **Logistic Regression** baseline to evaluate whether the
added model complexity is actually justified on this dataset.

## Dataset

- **Source:** [Cardiovascular Disease dataset](https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset) (Kaggle, by Svetlana Ulianova)
- **Size:** 70,000 patient records, 11 features + target
- **Target:** `cardio` — presence (1) or absence (0) of cardiovascular disease, well balanced (~50/50)
- **Features:**
  - *Objective:* age, height, weight, gender
  - *Examination:* systolic/diastolic blood pressure (`ap_hi`/`ap_lo`), cholesterol, glucose
  - *Subjective/lifestyle:* smoking, alcohol intake, physical activity

We initially evaluated a smaller Kaggle "Heart Disease Prediction" dataset (270 rows), but moved to this
larger dataset after finding that a small sample size didn't give a boosting algorithm enough data to
show a real advantage over a simpler linear model.

## Project Structure

```
├── cl_GB_XGBoost_Mini_Project.ipynb        # Main notebook (EDA, preprocessing, training, evaluation)
├── Cardio_XGBoost_Presentation.pptx        # Slide deck for the presentation
└── README_Cardio_XGBoost_Project.md        # This file
```

The notebook follows the required structure:
1. Introduction
2. Algorithm Explanation (Gradient Boosting / XGBoost)
3. Dataset Exploration (EDA)
4. Data Preprocessing
5. Model Training
6. Model Evaluation
7. Bonus: comparison with Logistic Regression
8. Conclusion

## How to Run

1. Open `cl_GB_XGBoost_Mini_Project.ipynb` in [Google Colab](https://colab.research.google.com) (File → Upload notebook).
2. Run all cells top to bottom (`Runtime → Run all`). The dataset is pulled directly from Kaggle via
   `kagglehub` — no manual download needed.
3. All models use `random_state=42`, so results should closely match the numbers reported below.

## Data Preprocessing Summary

The raw data has no missing values, but contains data quality issues that required cleaning:

| Issue | Fix |
|---|---|
| `age` stored in days | Converted to years |
| Impossible blood pressure values (negative, in the thousands, or `ap_hi` < `ap_lo`) | Filtered to clinically plausible ranges |
| Extreme height/weight outliers | Clipped to the 1st–99th percentile |
| `id` column | Dropped (not predictive) |

**Result:** 70,000 → 66,212 rows retained (3,788 rows / 5.4% removed).

No categorical encoding was needed — all features were already numeric, including the ordinal
`cholesterol` and `gluc` columns (1 = normal, 2 = above normal, 3 = well above normal).

## Results

**XGBoost** (test set, 13,243 patients):

| Metric | Score |
|---|---|
| Accuracy | 73.1% |
| Precision | 74.9% |
| Recall | 68.5% |
| F1 Score | 71.5% |

**Bonus comparison — XGBoost vs. Logistic Regression:**

| Model | Accuracy | Precision | Recall | F1 Score |
|---|---|---|---|---|
| Logistic Regression | 72.9% | 75.7% | 66.4% | 70.7% |
| **XGBoost** | **73.1%** | 74.9% | 68.5% | **71.5%** |

XGBoost outperformed Logistic Regression on accuracy, recall, and F1 — a modest but consistent margin,
made possible by the dataset being large enough (66,212 rows) for the boosting model to learn
non-linear patterns without overfitting.

## Key Findings

- **Blood pressure was the most important predictor** — but only after removing invalid readings; the
  raw data's correlation with the target was misleadingly close to zero.
- Age and cholesterol were also strong, medically-consistent predictors.
- Lifestyle habits (smoking, alcohol, activity) showed weak correlation with the target in this dataset.
- The model's precision is higher than its recall, meaning it misses more true disease cases than it
  falsely flags — a meaningful limitation in a medical screening context.

## Next Steps

- Hyperparameter tuning (GridSearchCV / Optuna) — the current model uses XGBoost's default settings
- Adjusting the classification threshold or class weights to reduce false negatives
- Feature engineering (e.g. BMI, pulse pressure = `ap_hi` − `ap_lo`)
- Trying `LightGBM` for comparison at this dataset size
