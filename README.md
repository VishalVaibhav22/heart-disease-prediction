# Heart Disease Prediction & Decision Analysis

An end-to-end machine-learning project comparing traditional classifiers and Bayesian Networks
for heart-disease prediction, with a three-page interactive Power BI dashboard.

## Project Highlights

- Combines 920 records from the four UCI Heart Disease datasets and evaluates on a stratified
  184-patient test set.
- Compares Logistic Regression, Random Forest, Categorical Naive Bayes, an MLP, and two Bayesian
  Networks (domain-designed and Hill-Climbing/BIC learned).
- Prevents preprocessing leakage by splitting before imputation and fitting imputation on training
  data only.
- Uses Variable Elimination and Likelihood Weighting for exact and approximate inference with
  partial evidence.
- Explores cost-sensitive thresholds, value of information, and sequential test selection.

## Test-Set Results

All metrics are from the held-out test set using a 0.5 classification threshold.

| Model | Accuracy | F1 | ROC-AUC |
|---|---:|---:|---:|
| Naive Bayes | **0.8207** | **0.8374** | **0.8918** |
| BN Hill Climb + BIC | 0.7989 | 0.8246 | 0.8879 |
| Domain Expert BN | 0.7935 | 0.8100 | 0.8850 |
| Random Forest | 0.7989 | 0.8213 | 0.8812 |
| Logistic Regression | 0.7989 | 0.8246 | 0.8782 |
| MLP Neural Net | 0.7446 | 0.7793 | 0.8599 |

Naive Bayes leads on accuracy and ROC-AUC, but the differences among the top five models are
small for a single test split. With partial Bayesian Network evidence, mean ROC-AUC rises from
0.677 using demographics to 0.888 with all 13 features.

The project also compares a utility-derived threshold of 0.167 with 0.5. Under the chosen reward
matrix, false negatives fell from 21 to 9 for the Domain Expert BN and from 15 to 5 for the learned
BN; these trade-offs depend on the assumed costs.

![Model comparison across evaluation metrics](results/model_comparison.png)

![Conditional value-of-information analysis](results/vpi_ranking.png)

## Power BI Dashboard

The PBIP report contains:

1. **Patient Insights** — aggregate demographics and disease prevalence.
2. **Model Performance** — six-model comparison and a model-selectable confusion matrix.
3. **Decision Analysis** — threshold trade-offs, evidence progression, value of information, and
   likelihood-weighting results.

![Patient Insights dashboard](screenshots/patient-insights.png)

Other existing captures: [Model Performance](screenshots/model-performance.png) ·
[Decision Analysis](screenshots/decision-analysis.png)

Open [`powerbi/Heart_Disease_Analytics.pbip`](powerbi/Heart_Disease_Analytics.pbip) in Power BI
Desktop to explore the report.

## Dataset

The project uses the [UCI Machine Learning Repository Heart Disease dataset (45)](https://archive.ics.uci.edu/dataset/45/heart+disease),
combining Cleveland, Hungarian, Switzerland, and VA records. The target maps diagnosis code 0 to
no disease and codes 1–4 to disease. Raw UCI files are not included; the notebook downloads them
when needed. See
[`data/README.md`](data/README.md) for attribution and dataset details.

## Run Locally

Install dependencies with `pip install -r requirements.txt`, then launch `jupyter lab` and run
[`notebooks/heart_disease_prediction.ipynb`](notebooks/heart_disease_prediction.ipynb) from top to
bottom. The final notebook cell exports the Power BI data to `results/powerbi/`.

To refresh the dashboard, set the semantic model's `AnalysisDataFolder` parameter to your local
`results/powerbi/` directory, then refresh in Power BI Desktop. The patient-level and sequential
diagnosis CSV exports are generated locally and excluded from Git.

## Project Structure

- `notebooks/` — analysis, modeling, inference, and Power BI exports.
- `powerbi/` — PBIP report, PBIR definitions, and TMDL semantic model.
- `results/` — analysis figures and aggregate Power BI CSV exports.
- `screenshots/` — dashboard page captures.
- `data/README.md` — UCI source, attribution, and download instructions.

## Technologies

Python · pandas · NumPy · scikit-learn · pgmpy · Matplotlib · Seaborn · NetworkX · JupyterLab ·
Power BI Desktop (PBIP/PBIR/TMDL)

## Limitations

The data is historical, has substantial missingness, and has not been externally validated.
Evaluation uses one train/test split without hyperparameter tuning. The cost-sensitive threshold
uses an assumed reward matrix, not clinical guidance. See the notebook and Power BI data dictionary
for further details. The model is not clinically validated and is not intended for diagnosis or
treatment.
