# Glucose ML Model

Predicting glucose levels 30 minutes ahead for people with Type 1 diabetes, from continuous glucose monitor (CGM), insulin, carb and activity data. Built in Spring 2026 for the Georgia Tech VIP Treatment Systems team (advisor: Dr. Doug Bodner), as the forecasting part of a diabetes management app.

## Data

[HUPA-UCM Diabetes Dataset](https://data.mendeley.com/datasets/3hbcscwz44): 25 people with Type 1 diabetes, recorded every 5 minutes. Each file in `data/preprocessed/` is one patient, with CGM glucose, basal and bolus insulin, carb intake, and calories, heart rate and steps from a fitness tracker. After removing incomplete rows, 7 patients (about 24,000 readings) had usable data.

## Method

All in `Multiple_Linear_Regression.ipynb` (scikit-learn):

1. **Cleaning:** merged the patient files, parsed timestamps, dropped empty rows and sorted each patient's readings by time.
2. **Target:** glucose 30 minutes ahead (6 readings later, per patient).
3. **Features:** current glucose, the three previous readings (5, 10 and 15 minutes earlier), hour, minute and day of week, insulin, carbs, calories, heart rate and steps.
4. **Evaluation:** split by patient, not by row, so the model is always tested on patients it never saw during training (5 training patients, 2 test patients).
5. **Models:** a naive persistence baseline (predict that glucose stays the same), linear regression, a random forest, and a random forest tuned with grid search.

## Results (held-out patients)

| Model | RMSE (mg/dL) | R² |
|---|---|---|
| Naive persistence | 33.08 | 0.832 |
| Linear regression | 31.21 | 0.854 |
| Random forest | 28.50 | 0.879 |
| **Random forest (tuned)** | **27.19** | **0.887** |

The tuned random forest cuts error by about 18% compared with the persistence baseline. Current glucose is by far the most important feature (about 0.88 of the forest's feature importance), followed by the glucose readings just before it.

## Limitations and next steps

- Only 7 patients had complete data, so the test set is 2 patients. Testing on more patients is the most important next step.
- Try models built for time series (for example gradient boosting or recurrent networks) and longer prediction horizons.

## Files

- `Multiple_Linear_Regression.ipynb`: the full pipeline, from loading to evaluation and plots
- `tuned_random_forest_glucose_model.pkl`, `glucose_model_scaler.pkl`, `glucose_model_features.json`: the saved model, scaler and feature list
- `data/preprocessed/`: one CSV per patient

(The folder name and notebook name come from the template this project started from.)
