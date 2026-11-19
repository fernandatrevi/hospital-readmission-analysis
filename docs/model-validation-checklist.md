# Model validation checklist

This checklist separates **completed exploratory work** from steps required before any clinical decision-support use.

## Current evidence
- Patient-grouped training/test split and grouped cross-validation are documented in the README.
- Balanced LightGBM test ROC AUC is reported as 0.6759, recall 57.00% and precision 17.39% at threshold 0.50.
- The test set was consulted during exploratory work; it is **not** a pristine final holdout.

## Next evaluation steps
- [ ] Freeze a prediction timestamp and exclude variables recorded after it.
- [ ] Audit cohort inclusion/exclusion criteria, including discharge disposition.
- [ ] Use a new untouched temporal or external test cohort.
- [ ] Report prevalence, average precision, ROC AUC, sensitivity, specificity, PPV, NPV and confidence intervals.
- [ ] Plot calibration and assess Brier score before treating outputs as probabilities.
- [ ] Evaluate thresholds at realistic follow-up capacities (e.g. alerts per 100 encounters).
- [ ] Examine error rates and calibration across available subgroups; report sample sizes.
- [ ] Document missingness, drift, label definitions and limits to generalization.
- [ ] Obtain clinical, privacy and governance review before deployment.

## Reproducibility acceptance criteria
A separate reviewer should be able to locate the dataset source and license, reproduce the patient-grouped split, execute the notebooks in sequence, and regenerate metrics from clearly versioned dependencies. Do not report this checklist as completed tests.
