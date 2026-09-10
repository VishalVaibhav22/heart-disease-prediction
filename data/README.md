# Data

## Source

UCI Machine Learning Repository, **Heart Disease** (dataset 45).

- Dataset page: <https://archive.ics.uci.edu/dataset/45/heart+disease>
- Directory the notebook downloads from: <https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/>

Original donors: Andras Janosi (Hungarian Institute of Cardiology, Budapest), William Steinbrunn
(University Hospital, Zurich), Matthias Pfisterer (University Hospital, Basel) and Robert Detrano
(V.A. Medical Center, Long Beach and Cleveland Clinic Foundation).

## Required files

The notebook uses the four `processed.*` files, which contain the 14-column subset that published
work on this dataset generally uses. All four are combined into a single table.

| File | Institution | Records |
|---|---|---|
| `processed.cleveland.data` | Cleveland Clinic Foundation | 303 |
| `processed.hungarian.data` | Hungarian Institute of Cardiology, Budapest | 294 |
| `processed.switzerland.data` | University Hospital, Zurich | 123 |
| `processed.va.data` | V.A. Medical Center, Long Beach | 200 |
| **Total** | | **920** |

They belong in this directory, next to this file:

```
data/
├── README.md
├── processed.cleveland.data
├── processed.hungarian.data
├── processed.switzerland.data
└── processed.va.data
```

## How to get them

**Nothing to do in most cases.** The loader in the notebook checks this directory first, and
downloads any missing file straight from the UCI URL above, caching it here. Running the notebook
on a fresh clone with a working internet connection is enough.

The notebook resolves this directory from the repository root rather than from its own working
directory, so it finds the files whether the kernel starts in `notebooks/` or at the repository
root.

To fetch them by hand instead, download the four files from the UCI directory link above, or:

```bash
cd data
curl -O https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/processed.cleveland.data
curl -O https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/processed.hungarian.data
curl -O https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/processed.switzerland.data
curl -O https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/processed.va.data
```

## Why the files are not committed

They are listed in `.gitignore`. They are not ours to redistribute, the notebook fetches them
automatically, and keeping a single canonical copy at UCI avoids this repository drifting from the
official version. They are small (about 40 KB in total), so this is about provenance rather than
size.

## File format

No header row. Comma-separated. Missing values are written as `?`. Columns in order:

| # | Column | Description | Values |
|---|---|---|---|
| 1 | `age` | Age in years | continuous |
| 2 | `sex` | Sex | 0 = female, 1 = male |
| 3 | `cp` | Chest pain type | 1 = typical angina, 2 = atypical angina, 3 = non-anginal pain, 4 = asymptomatic |
| 4 | `trestbps` | Resting blood pressure on admission (mm Hg) | continuous |
| 5 | `chol` | Serum cholesterol (mg/dL) | continuous |
| 6 | `fbs` | Fasting blood sugar > 120 mg/dL | 0 = false, 1 = true |
| 7 | `restecg` | Resting electrocardiogram | 0 = normal, 1 = ST-T wave abnormality, 2 = left ventricular hypertrophy |
| 8 | `thalach` | Maximum heart rate achieved | continuous |
| 9 | `exang` | Exercise-induced angina | 0 = no, 1 = yes |
| 10 | `oldpeak` | ST depression induced by exercise relative to rest | continuous |
| 11 | `slope` | Slope of the peak exercise ST segment | 1 = upsloping, 2 = flat, 3 = downsloping |
| 12 | `ca` | Major vessels coloured by fluoroscopy | 0–3 |
| 13 | `thal` | Thallium perfusion scan | 3 = normal, 6 = fixed defect, 7 = reversible defect |
| 14 | `num` | Diagnosis of heart disease | 0–4 |

Note that `cp`, `slope` and `thal` are **not** zero-based in these files. `thal` uses the codes
3, 6 and 7. The notebook declares these domains explicitly and asserts against them after
preprocessing.

## Things to know before using this data

Two properties of these files matter for preprocessing and are handled explicitly in the notebook:

- **`chol = 0` and `trestbps = 0` mean "not measured", not a real reading.** All 123 Switzerland
  patients and 49 VA patients have `chol = 0`. These are recoded as missing.
- **`oldpeak = 0` is a genuine, common measurement**, meaning zero ST depression. 370 of the
  858 recorded values are exactly 0, and those patients have a 34% disease rate against 70%
  for the rest.
  Treating these zeros as missing destroys real signal, so the notebook leaves them alone.

Missing-value counts across the combined 920 records:

| Column | Missing | % |
|---|---|---|
| `ca` | 611 | 66.4 |
| `thal` | 486 | 52.8 |
| `slope` | 309 | 33.6 |
| `fbs` | 90 | 9.8 |
| `oldpeak` | 62 | 6.7 |
| `trestbps` | 59 | 6.4 |
| `thalach` | 55 | 6.0 |
| `exang` | 55 | 6.0 |
| `chol` | 30 | 3.3 |
| `restecg` | 2 | 0.2 |

`ca`, `thal` and `slope` are missing largely because the non-Cleveland sites did not record them.
Imputing two thirds of a column with a single value makes it close to a constant, so results
involving `ca` and `thal` should be read with that in mind.

## Target

The notebook binarises `num` into `target`: `0` stays `0` (no disease), and `1–4` all become `1`
(disease present). This gives a 55.3% disease rate across the 920 records.
