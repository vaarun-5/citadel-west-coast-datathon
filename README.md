## Citadel Data Open (Spring 2022)

### Predicting Hospital Readmission Risk and Future Costs

This repository explores predictive modeling on a diabetes readmission dataset to:
- Predict whether a patient will be readmitted (binary classification)
- Forecast a patient’s future healthcare cost (regression)

The work is organized as a set of Jupyter notebooks that perform data cleaning, feature engineering, exploratory analysis, modeling, and benchmarking. Supporting figures and intermediate datasets are included.

---

### Repository structure

- `datasets/`: Raw inputs and mapping files
  - `diabetes_data_initial.csv`, `id_mapping.csv`, `admission_source.csv`, `admission_type.csv`, `discharge_map.csv`, `medicine_costs.csv`, `pima_indians.csv`
- `notebooks/`: Main analysis notebooks
  - `Diabetes.ipynb`, `vaarun.ipynb`, `aryan*.ipynb`, `classify.ipynb`, `peter.ipynb`, etc.
- `output/`: Generated artifacts
  - `data.csv` (engineered feature matrix with future cost targets)
- `img/`: Exported figures (feature importances, SMOTE diagnostics, benchmarking, cost proportions, service coverage)
- `requirements.txt`: Minimal Python dependencies used across notebooks
- `README.md`: This document

---

### Data

- Primary dataset: `datasets/diabetes_data_initial.csv`
  - Patient-encounter level records with demographics, diagnoses, procedure and medication signals, payer information, and readmission label.
- Mapping/reference files:
  - `id_mapping.csv`, `admission_source.csv`, `admission_type.csv`, `discharge_map.csv`
- Cost reference: `medicine_costs.csv` and related assumptions used to derive downstream cost targets.

The engineered, modeling-ready table is written to `output/data.csv`. It contains:
- Features derived from the raw dataset and mappings
- Label columns including:
  - `readmitted` (0/1 after mapping NO/>30→0, <30→1)
  - Future cost targets such as `future_cost_total`, `future_med_cost`, `future_lab_procedure_cost`, `future_procedure_cost`, `future_emergency_cost`

---

### Preprocessing and feature engineering

Performed primarily in `notebooks/Diabetes.ipynb` and `notebooks/vaarun.ipynb`:
- Age bin encoding: map bracketed ages (e.g., `[50-60)`) to numeric midpoints
- Deduplication: drop duplicate `patient_nbr` encounters; drop rows with missing critical values
- Medical specialty grouping: collapse sparse `medical_specialty` values into categories (`pediatrics`, `psychic`, `neurology`, `surgery`, `high_freq`, `low_freq`, `ungrouped`, `missing`)
- Diagnosis normalization: map ICD-9 codes to coarse diagnostic categories (e.g., `circulatory`, `respiratory`, `digestive`, `diabetes`, `injury`, `musculoskeletal`, `genitourinary`, `neoplasms`, `pregnancy`, `other`)
- Readmission label mapping: `readmitted` → {0,1} where `NO` or `>30` → 0, `<30` → 1
- Class imbalance handling: use SMOTE (see figures `img/before_smote.png` and `img/after_smote.png`) where appropriate in classification experiments

---

### Modeling

Implemented and explored in `notebooks/classify.ipynb` and other classifier notebooks:
- Readmission classification
  - Baselines and tree-based ensembles (e.g., `RandomForestClassifier`)
  - Optional SMOTE for minority-class upsampling
- Cost regression
  - Gradient Boosting Regressor to predict `future_cost_total`
  - Two-stage approach: first classify readmission, then predict costs for likely readmissions (set to zero otherwise)
- Evaluation
  - Classification: typical metrics (accuracy/AUC, confusion insights)
  - Regression: MSE, RMSE, MAE (e.g., sample RMSE ≈ 4.6k–12k shown in experiments)
  - Benchmarking and feature importance visualizations in `img/`

Note: Some notebooks iterate on hyperparameters and model variants; images such as `feature_importances.png`, `benchmarking.png`, and `cost_proportions.png` summarize key insights.

---

### Getting started

#### 1) Environment

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\\Scripts\\activate
pip install -r requirements.txt
# Common scientific stack (if not already installed by your environment)
pip install pandas numpy scikit-learn matplotlib seaborn tqdm jupyterlab
```

#### 2) Open the notebooks

```bash
jupyter lab  # or: jupyter notebook
```

Recommended flow:
- Run `notebooks/Diabetes.ipynb` or `notebooks/vaarun.ipynb` to perform preprocessing and write `output/data.csv`.
- Open `notebooks/classify.ipynb` to train and evaluate the classification and regression models on `output/data.csv`.

> If paths differ on your machine, adjust relative paths near the top of each notebook (e.g., `datasets/` and `output/`).

---

### Reproducing results

- Ensure `datasets/` contains the raw CSVs listed above
- Execute preprocessing notebooks end-to-end to regenerate `output/data.csv`
- Execute modeling notebooks (e.g., `classify.ipynb`) to reproduce metrics and figures

Figures are written under `img/` when applicable.

---

### Notes and assumptions

- ICD-9 category mappings are simplified into coarse diagnostic buckets (see notebook code for the exact rules). Reference for ICD-9 categories is based on publicly available coding ranges (e.g., AAPC ICD-9 overviews).
- Cost targets (`future_*`) are derived using provided cost assumptions/mappings and may reflect simplifying assumptions for the competition context.
- This repository contains multiple team members’ notebooks; some code paths are exploratory and intended for comparison rather than production.

---

### Acknowledgments

- Dataset: diabetes readmission dataset used for Citadel Data Open (commonly associated with the UCI “Diabetes 130-US hospitals” dataset)
- ICD-9 references informed by coding guidelines (e.g., AAPC)
- Thanks to the organizers and collaborators whose notebooks are included (`Ani`, `Aryan`, `Peter`, `Vaarun`)

---

### License

This repository is provided for educational and research purposes. If you plan to reuse or redistribute, please check competition rules and any dataset-specific licensing terms.