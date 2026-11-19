# Reproducibility and validation checklist

This project analyzes hospital encounters for early (under 30 days) readmission. It is a research and portfolio exercise, not a clinical decision support system.

## Reproduce responsibly

1. Obtain the original diabetes encounter dataset from its authorized source. Do not commit patient-level datasets.
2. Create an isolated Python environment and install dependencies according to the repository's environment files.
3. Run the notebooks in numbered order from `notebooks/`, documenting package versions and the random seed.
4. Confirm that the outcome is early readmission (`<30`) versus later or no recorded readmission.
5. Split **by patient identifier**, not by encounter, before fitting preprocessing or models. Verify that training and test patient IDs do not overlap.
6. Fit imputers, encoders, feature selection, and weighting using training data only. Use patient-grouped folds during cross-validation.
7. Record ROC AUC, precision, recall, F1, confusion matrix, test prevalence, and the selected classification threshold.

## Validation before any real-world use

- Assess precision-recall curves and calibration, especially at low event prevalence.
- Report sensitivity and false alerts per 100 encounters at operationally realistic thresholds.
- Check model stability by hospital, time period, and patient subgroups when permitted and sufficiently powered.
- Audit potential label leakage from post-discharge variables.
- Evaluate the costs of missed readmissions, false alerts, and alert fatigue with clinicians.
- Seek external and prospective validation and a privacy/legal review before clinical deployment.

## Reporting boundaries

The existing reported test results are historical experimental results, not independently re-run by this document. Do not describe ROC AUC as the probability of a correct bedside decision. A research model with substantial false positives must not be marketed as clinically validated.
