# Power BI export data dictionary

These files are descriptive and educational exports from
`notebooks/heart_disease_prediction.ipynb`. They are intended to support interactive Power BI
reports; they do not change the notebook's machine-learning pipeline or its results. CSVs are
written to this directory by the notebook's final **Power BI Data Export** cell. Run the notebook
from top to bottom first so that the cell uses its actual in-memory data and results.

## Files and sources

| File | Notebook source | Purpose |
|---|---|---|
| `powerbi_patients.csv` | `raw_df`, `ZERO_IS_MISSING`, `BIN_EDGES` | One descriptive row per original combined patient, with original diagnosis codes, a derived binary outcome, institution, and readable category labels. |
| `powerbi_model_metrics.csv` | `results_df` | Held-out test-set comparison of all six models at the notebook's standard 0.5 threshold. |
| `powerbi_confusion_matrix.csv` | `predictions`, `y_test` | Long-form 2-by-2 confusion matrix cells for each of the six models, calculated with labels 0 and 1 at threshold 0.5. |
| `powerbi_threshold_comparison.csv` | `threshold_df` | Actual cost/reward comparison for both Bayesian Networks at both evaluated thresholds. |
| `powerbi_evidence_stages.csv` | `stage_summary` | Learned Bayesian Network results as progressively more evidence is observed across the test set. |
| `powerbi_voi_ranking.csv` | `voi` | Information gain and Value of Perfect Information (VPI) for each candidate test under the notebook's starting evidence. |
| `powerbi_sequential_diagnosis.csv` | `diagnosis_runs` | The actual step-by-step demonstration runs for both example patients. |
| `powerbi_likelihood_weighting.csv` | `lw_df` | Likelihood Weighting estimates and absolute errors versus exact Variable Elimination for the notebook's sample sizes. |

## Patient-level data

`patient_id` is a synthetic, sequential row identifier starting at 1. It is generated for
convenient Power BI row identification and is **not** an original UCI patient identifier.
`source` identifies the contributing institution. The original numerical age and clinical
measurements come from `raw_df`; they are not taken from the discretised `train` or `test` tables.
This keeps patient-level descriptions in the original measurement units and preserves actual
ages instead of replacing them with model bins.

`diagnosis_num` is the original UCI diagnosis code (0–4). `heart_disease` is derived from it:
**0 = no disease** (original code 0), and **1 = disease** (original codes 1–4).
`heart_disease_label` renders those values as “No Disease” and “Disease.”

The notebook's `ZERO_IS_MISSING` rules are applied to this descriptive export: zero in `chol`,
`trestbps`, or `thalach` means the measurement was not recorded and is exported as missing.
No descriptive imputation is performed. `oldpeak = 0` is retained because zero ST depression is a
valid, common measurement, not a missing-value code.

The original patient-level columns mean:

| Column | Meaning |
|---|---|
| `age` | Age in years, retained as the original numerical value. |
| `sex` | Sex code: 0 = Female; 1 = Male. |
| `cp` | Chest-pain code: 1 = Typical Angina; 2 = Atypical Angina; 3 = Non-Anginal Pain; 4 = Asymptomatic. |
| `trestbps` | Resting blood pressure on admission, in mm Hg. |
| `chol` | Serum cholesterol, in mg/dL. |
| `fbs` | Fasting blood sugar above 120 mg/dL: 0 = False; 1 = True. |
| `restecg` | Resting ECG code: 0 = Normal; 1 = ST-T wave abnormality; 2 = Left ventricular hypertrophy. |
| `thalach` | Maximum heart rate achieved. |
| `exang` | Exercise-induced angina: 0 = No; 1 = Yes. |
| `oldpeak` | ST depression induced by exercise relative to rest. |
| `slope` | Peak-exercise ST segment slope: 1 = Upsloping; 2 = Flat; 3 = Downsloping. |
| `ca` | Number of major vessels coloured by fluoroscopy (0–3). |
| `thal` | Thallium/perfusion scan code: 3 = Normal; 6 = Fixed defect; 7 = Reversible defect. |
| `diagnosis_num` | Original UCI diagnosis code, 0–4. |
| `source` | Institution associated with the source record. |
| `patient_id` | Synthetic row number; not an original UCI identifier. |
| `heart_disease` | Binary outcome derived from `diagnosis_num`: 0 = No Disease; 1 = Disease. |

Power BI-friendly category columns retain the corresponding original numeric columns:

| Column | Meaning |
|---|---|
| `sex_label` | `sex`: 0 = Female; 1 = Male. |
| `cp_label` | `cp`: 1 = Typical Angina; 2 = Atypical Angina; 3 = Non-Anginal Pain; 4 = Asymptomatic. |
| `heart_disease_label` | `heart_disease`: 0 = No Disease; 1 = Disease. |
| `age_group` | Original age grouped using the notebook's `[-infinity, 40, 55, infinity]` boundaries: 40 or below; 41–55; above 55. |
| `fbs_label` | `fbs`: 0 = False; 1 = True (fasting blood sugar above 120 mg/dL). |
| `restecg_label` | `restecg`: 0 = Normal; 1 = ST-T wave abnormality; 2 = Left ventricular hypertrophy. |
| `exang_label` | `exang`: 0 = No; 1 = Yes (exercise-induced angina). |
| `slope_label` | `slope`: 1 = Upsloping; 2 = Flat; 3 = Downsloping. |
| `thal_label` | `thal`: 3 = Normal; 6 = Fixed defect; 7 = Reversible defect. |

## Model and inference exports

`powerbi_model_metrics.csv` contains `Model`, `Accuracy`, `Precision`, `Recall`, `F1`, and
`ROC_AUC`. The notebook evaluates all six models on the same held-out 184-patient test set; these
are measured results, not training-set metrics or estimated values. The reported labels use the
standard 0.5 decision threshold. Accuracy is the share of test predictions that are correct;
precision is the share of predicted disease cases that are truly disease-positive; recall is the
share of actual disease-positive cases detected; F1 is the harmonic mean of precision and recall;
ROC-AUC measures ranking discrimination across probability thresholds.

`powerbi_confusion_matrix.csv` contains `Model`, `Threshold`, `Actual`, `Predicted`, and `Count`.
`Actual` and `Predicted` are readable disease labels. There are four actual/predicted combinations
for each model; `Threshold` is 0.5. `Count` is the number of held-out patients in that combination.

`powerbi_threshold_comparison.csv` contains `Model`, `Threshold`, `Accuracy`, `Recall`,
`Precision`, `FN`, `FP`, `Total_Reward`, and `Reward_Per_Patient`. This comparison has four rows:
the standard and utility-optimal thresholds for each of the two Bayesian Networks. It does not
include the four baseline classifiers because the notebook's cost-based threshold analysis is
defined for the Bayesian Network probabilities and uses its existing reward matrix; that matrix
and its results are not recalculated for this export. `FN` and `FP` are false-negative and
false-positive counts; reward columns are the notebook's actual totals and per-test-patient values.

`powerbi_evidence_stages.csv` contains `Stage`, `Evidence_Count`,
`Mean_P_Disease_True_1`, `Mean_P_Disease_True_0`, `Test_Accuracy`, and `ROC_AUC`. Its four stages
are `Demographics`, `+ Consultation`, `+ Exercise test`, and `Full record`, using the actual stage
names and values from `stage_summary`. The mean probability columns are grouped by the true test
label; `Evidence_Count` is the number of observed features at that stage.

`powerbi_voi_ranking.csv` contains `Feature`, `Information_Gain_Bits`, and `VPI`. This is a
**conditional, patient-specific analysis**, not a population-wide ranking. Its starting evidence
is the age and sex evidence for the first disease-positive patient in the held-out test set, as
used by the notebook's `start_evidence` variable. The Bayesian Network uses the discretised age
state in this evidence; the descriptive patient export separately retains actual ages.

`powerbi_sequential_diagnosis.csv` contains `Patient_Label`, `Step`, `Test_Ordered`,
`Number_Observed`, `Probability_Disease`, `Decision`, and `Reward`. It combines the two actual
demonstration runs in `diagnosis_runs`; the decision values retain the notebook's 0/1 coding.
`Step` is the sequential decision step, `Test_Ordered` is the feature added at that step (or `-`
for the initial evidence), and `Reward` is the result under the notebook's reward matrix.

`powerbi_likelihood_weighting.csv` contains `Number_of_Samples`,
`Likelihood_Weighting_Estimate`, and `Absolute_Error`. It compares the estimates in `lw_df` with
the exact Variable Elimination posterior for the same query. Sample sizes are taken directly from
the notebook results; `Absolute_Error` is the absolute difference from that exact posterior.

## Limitations

This is an educational analysis using a historical research dataset, not a clinical diagnostic
system. The results have not been validated on an external population and must not be used to
diagnose or guide care.
