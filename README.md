# Hospital Readmission Analysis

Exploratory analysis and classification of hospital readmissions within 30 days among patients with diabetes.

## Objective

Explore readmission patterns, compare classification models, and evaluate the trade-off between detecting early readmissions and generating false alerts.

## Dataset

The analysis uses `diabetic_data.csv`, downloaded from Kaggle.

- Hospital encounters: 101,766.
- Unique patients: 71,518.
- Early readmissions: 11,357, approximately 11.16% of encounters.
- Unit of analysis: a hospital encounter, rather than a unique patient.

Target definition:

- `1`: readmission within 30 days (`<30`).
- `0`: later readmission (`>30`) or no recorded readmission (`NO`).

The dataset is stored locally and is excluded from Git.

## Tools

Python, pandas, scikit-learn, PyCaret 3.3.2, LightGBM, Matplotlib, Jupyter notebooks, VS Code, and Git.

## Workflow

1. Explore missing values, readmission frequency, age, length of stay, and prior hospitalizations.
2. Separate training and test data by patient to prevent patient overlap.
3. Remove identifiers, the original outcome column, weight, and predictors that are constant in training.
4. Configure preprocessing and three-fold patient-grouped cross-validation.
5. Compare five classifiers by cross-validation ROC AUC.
6. Evaluate class weighting in LightGBM.
7. Report test metrics and the confusion matrix.

The split contains 81,613 training encounters and 20,153 test encounters, with no shared patients.

The comparison uses 42 predictors: 8 numerical and 34 categorical.

## Model comparison

Mean results from three-fold patient-grouped cross-validation:

| Model | ROC AUC | Recall | Precision |
|---|---:|---:|---:|
| LightGBM | 0.6696 | 2.01% | 47.97% |
| Logistic Regression | 0.6651 | 2.74% | 44.86% |
| Random Forest | 0.6456 | 0.21% | 59.40% |
| Extra Trees | 0.6308 | 0.27% | 38.13% |
| Decision Tree | 0.5309 | 18.32% | 16.10% |

LightGBM achieved the highest ROC AUC among these five configurations. However, its recall was low at the default classification threshold.

Using balanced class weights increased LightGBM's mean validation recall to 55.09%, while precision decreased to 18.25%. Its mean ROC AUC was 0.6673.

## Balanced LightGBM: test results

Results at a classification threshold of 0.50:

| Metric | Value |
|---|---:|
| ROC AUC | 0.6759 |
| Accuracy | 66.52% |
| Recall | 57.00% |
| Precision | 17.39% |
| F1 score | 0.2665 |

| Actual outcome | Predicted no early readmission | Predicted early readmission |
|---|---:|---:|
| No early readmission | 12,179 | 5,823 |
| Early readmission | 925 | 1,226 |

The model detected 1,226 of 2,151 early readmissions and missed 925. It generated 5,823 false alerts.

Class weighting increased detection, but the number of false alerts remains a major limitation. Accuracy alone is insufficient because most encounters have no early readmission.

## Notebooks

- `notebooks/01_data_exploration.ipynb`: data quality and exploratory analysis.
- `notebooks/02_readmission_modeling.ipynb`: preprocessing and baseline logistic regression.
- `notebooks/03_model_comparison.ipynb`: PyCaret comparison, class weighting, and test evaluation.

## Run the model comparison

Use Python 3.11 and the recorded dependencies.

```bash
conda create -n hospital-pycaret python=3.11 pip -y
conda activate hospital-pycaret
python -m pip install -r requirements-pycaret.txt
python -m ipykernel install --user --name hospital-pycaret --display-name "Hospital PyCaret (Python 3.11)"
```

On macOS, LightGBM may require OpenMP. With Homebrew installed:

```bash
brew install libomp
```

Place the downloaded CSV at:

```text
data/raw/diabetic_data.csv
```

Open `notebooks/03_model_comparison.ipynb` in VS Code, select the Hospital PyCaret kernel, and execute the cells in order. The dataset path assumes the notebook runs from the `notebooks` directory.

`requirements-pycaret.txt` records the development environment; installation on other platforms has not yet been verified.

## Limitations and future improvements

These results are exploratory and do not establish clinical usefulness.

### Evaluation limitations

- The test partition was previously evaluated during baseline exploration. These results should not be described as a completely untouched final evaluation.
- Exploratory analysis used the full dataset before model comparison.
- Grouping by patient prevents patient overlap, but does not demonstrate performance at another hospital or in a future time period.
- Balanced class weights can affect probability calibration. Model scores should not be interpreted as calibrated clinical risks.
- Eligibility rules for deaths and hospice discharges have not yet been reviewed.

### What I would change next

1. Review cohort eligibility and predictor availability at the intended prediction time, including death and hospice discharge categories.
2. Compare models at the same alert volume to measure detection under a common follow-up capacity.
3. Select classification thresholds using validation data only.
4. Add Average Precision and precision-recall curves to evaluate the minority class.
5. Evaluate hyperparameter tuning and additional features within patient-grouped validation.
6. Examine errors across age and other demographic groups.
7. Assess probability calibration.
8. Use additional independent data for a final evaluation and external validation.
9. Verify installation and execution in a fresh environment.