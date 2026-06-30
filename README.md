# Characterization of clinical-genetic factors associated with α-synuclein SAA in Parkinson's disease using Explainable AI

MSc Bioinformatics thesis — University of Murcia (UMU), 2026.
Author: **David Palomino Plantón**

This project characterises the clinical, demographic and genetic factors associated with the result of the **α-synuclein Seed Amplification Assay (αSyn-SAA)** in Parkinson's disease, comparing the **24 h** and **150 h** incubation protocols. It combines classical statistical modelling, multivariate factor analysis and an interpretable machine-learning model built with Explainable AI (XAI) techniques.

---

## Overview

The αSyn-SAA is a biomarker assay that detects misfolded α-synuclein, a hallmark of Parkinson's disease. Using the PPMI / AMP-PD cohorts, this work:

1. Builds an integrated dataset linking clinical, demographic, genetic (mutation/APOE) and clinical-scale data (MDS-UPDRS-III, Hoehn & Yahr, MoCA) to each participant's SAA result.
2. Identifies which factors are statistically associated with a positive SAA result, using **Firth penalized logistic regression** (robust to separation in small/imbalanced data) and **multivariate factor analysis** (FAMD, MCA).
3. Trains a **Random Forest** classifier to predict SAA status and interprets its predictions with **SHAP**, validating that the model relies on clinically meaningful features.
4. Compares the 24 h and 150 h protocols throughout.

---

## Data availability and governance

> **Raw data is not included in this repository.**
> The clinical and genetic data come from the **PPMI (Parkinson's Progression Markers Initiative)** and **AMP-PD** cohorts, which are **controlled-access** resources. Access requires an approved data use agreement, and redistribution of the raw data is not permitted under those terms.

This repository contains **only the analysis code**. To reproduce the work, request access to the cohorts through their official portals and place the resulting CSV files in a local `data/` folder (excluded from version control via `.gitignore`).

---

## Repository structure

```
parkinsons-asyn-saa-xai/
├── 01_data_preparation/      # Python (pandas) — builds the analysis dataset, run in order
│   ├── 01_merge_clinical_saa.ipynb      # Merge clinical records with SAA assay results; normalise visit/patient IDs
│   ├── 02_variable_selection.ipynb      # Split 24h / 150h protocols; drop inconclusive/undetermined; keep baseline
│   ├── 03_add_genetic_mutations.ipynb   # Add genetic mutation / APOE data; build age groups
│   ├── 04_add_clinical_scores.ipynb     # Add MDS-UPDRS-III, Hoehn & Yahr and MoCA scores
│   ├── 05_merge_protocols.ipynb         # Remove SWEDD arm; finalise age groups; concatenate protocols
│   ├── 06_impute_scores.ipynb           # Impute 0 for Healthy Controls in clinical scores; drop remaining NaNs
│   └── 07_final_cleaning.ipynb          # Final removal of incomplete rows
│
├── 02_factorial_analysis/    # R (R Markdown) — statistics & multivariate analysis
│   └── factorial_analysis.html          # Firth regression, forest plots, correlation matrices, FAMD, MCA
│
└── 03_ml_model_shap/         # Python (scikit-learn) — predictive model & explainability
    └── ml_model_shap.html               # Random Forest + GridSearchCV, SHAP global & per-individual explanations
```

The data-preparation notebooks are numbered and **must be run in order**: each step reads the output of the previous one.

---

## Methods

### 1. Data preparation (Python · pandas)
Integration of multiple PPMI/AMP-PD tables into a single analysis-ready dataset: merging clinical and SAA tables on patient/visit, harmonising visit codes (e.g. screening → baseline), adding genetic mutation and APOE status, attaching clinical scores, and a deliberate imputation strategy (clinical scores set to 0 for Healthy Controls, who by definition lack symptoms; remaining missing values dropped). Produced separately for the 24 h and 150 h protocols, then combined.

### 2. Statistical and multivariate analysis (R · FactoMineR, logistf, missMDA)
- **Firth penalized logistic regression** (`logistf`) for each protocol, to identify factors associated with SAA status while controlling for separation in imbalanced data, summarised in **forest plots**.
- **Spearman correlation matrices** to inspect collinearity among variables.
- **FAMD (Factor Analysis of Mixed Data)** to explore structure across the mixed numeric/categorical variables, with missing-data handling via `missMDA` (`imputeFAMD`) and a custom rule for Healthy Controls.
- **MCA (Multiple Correspondence Analysis)** on the categorical variables, with biplots and individual/variable maps.
- A **positive-control** supplementary analysis (screeplots, variable contributions) to validate the factorial pipeline.

### 3. Predictive model and explainability (Python · scikit-learn, SHAP)
- **Random Forest** classifier predicting SAA status (Positive vs Negative), tuned with `GridSearchCV` over a stratified 5-fold cross-validation, optimising **balanced accuracy**.
- Evaluation via confusion matrix and classification report on a held-out stratified test set.
- **SHAP (TreeExplainer)** for model interpretation: global feature importance (bar and beeswarm summary plots) and **per-individual waterfall plots**, with a check confirming the explained individuals match their underlying records.

---

## Key results

- Sample sizes: 24 h protocol = **231** participants; 150 h protocol = **810** participants.
- Random Forest accuracy (test set): **0.86**.
- Most influential features by SHAP: **MDS-UPDRS-III Score, Hoehn & Yahr stage, LRRK2 Mutation**.
- Main contrast between the 24 h and 150 h protocols: **The 150h protocol for the $\alpha$Syn-SAA assay generally provides a tighter distinction between positive and negative SAA status.**.

---

## Tech stack

**Python:** pandas, NumPy, scikit-learn, SHAP, matplotlib, joblib
**R:** FactoMineR, factoextra, logistf, missMDA, corrplot, ggplot2, dplyr, tidyr, ggrepel, ggforce, patchwork, readr

See `requirements.txt` for the Python environment.

---

## Author

**David Palomino Plantón** — MSc in Bioinformatics, University of Murcia.

Email: davidpalplanton@gmail.com
LinkedIn: www.linkedin.com/in/davidpalplanton
