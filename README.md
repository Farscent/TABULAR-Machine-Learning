# Tabular Machine Learning Handbook

A personal learning and syntax reference for Python, tabular data analysis, preprocessing, modeling, and evaluation. It starts with fundamentals and progresses to leakage-safe pipelines, model selection, and interpretation. It is intended for learners who know basic programming concepts and want explanations alongside reusable code, including preparation for future data-science competitions.

## Repository contents and learning order

| Notebook | Coverage |
|---|---|
| `00_Python_Fundamentals.ipynb` | Python containers and control flow; NumPy; Pandas; plotting; syntax tables | 
| `01_Exploratory_Data_Analysis.ipynb` | Training-only inspection, quality, statistics, charts, correlation, and EDA decisions |
| `02_Data_Preprocessing.ipynb` | Splits, missing values, encoding, scaling, outliers, features, selection, pipelines, and artifacts | 
| `03_Modeling.ipynb` | Baselines, linear/logistic models, trees, forests, boosting, CV/search, model selection, saving | 
| `04_Model_Evaluation.ipynb` | Regression/classification metrics, CV, validation comparison, interpretation, final test reports |

Each notebook includes learning objectives, explained techniques, runnable examples, interpretations, common mistakes, a Quick Syntax Reference, and three practice exercises. Read the first time in order; later use the headings and tables as a reference.

```text
TABULAR machine learning/
├── 00_Python_Fundamentals.ipynb
├── 01_Exploratory_Data_Analysis.ipynb
├── 02_Data_Preprocessing.ipynb
├── 03_Modeling.ipynb
├── 04_Model_Evaluation.ipynb
├── data/
│   ├── raw/nusantaramart_simple_sales.csv
│   └── processed/                 # generated splits, schema, reference exports
├── models/                        # generated pipelines, metadata, reports
├── README.md
└── requirements.txt
```

The source workspace contained a consulting workshop notebook and its CSV, but no accessible existing repository named `TABULAR machine learning`. This handbook was therefore created in a new subfolder of the workspace. Existing source files were preserved. The supplied CSV was copied unchanged into `data/raw/`.

## Data and task definitions

**Primary end-to-end regression:** NusantaraMart store sales. The supplied CSV has 86 stores and four columns: `store_id`, `region`, `promo_spend_million`, and `monthly_sales_million`. Both financial columns use IDR million. Predict monthly sales using promotional spending and region; retain store IDs for traceability but exclude them from features. The file has no timestamps or profit margins. Its provenance and collection period are not documented in the supplied workspace; do not assume it represents all future stores or establishes causal marketing effectiveness.

**Secondary classification examples:** Scikit-learn's built-in Wisconsin diagnostic breast-cancer dataset (569 rows, 30 numeric measurements), available offline through `load_breast_cancer`. We recode the positive target as `1 = malignant`, `0 = benign`. This case teaches classification syntax and evaluation; it is not medical guidance or a validated clinical system. NumPy/Pandas syntax and feature-engineering demonstrations also use clearly labeled constructed teaching tables.

### Evaluation contract

- Primary split: random state 42, 20% test, then 25% of the remaining rows for validation: **51 training / 17 validation / 18 test** stores.
- Notebook 01 explores only raw training rows. Notebook 02 repeats the exact split and fits demonstration transformations on training only.
- Notebook 03 loads **raw typed splits**, not pretransformed arrays, so all CV pipelines learn preprocessing inside each fold. Tuning uses training CV; algorithm selection uses validation MAE.
- After selection, the primary model and baseline are refitted on training plus validation (68 rows). Earlier training-only models are retained for honest validation analysis.
- The separate classifier uses a stratified 80/20 holdout, training-only stratified CV ROC-AUC for model selection, and a fixed threshold of 0.5.
- Notebook 04 opens each final holdout only after choices are locked. It compares the final regression model with the fixed baseline, and reports the selected classifier's holdout results. Do not tune models or thresholds to these test outcomes.
- Random row splitting assumes independent stores/observations. For future-month forecasting, repeated customers/stores, or grouped competition data, replace this with an appropriate time/group strategy.

## Installation

Use Python 3.10–3.13 in an isolated environment. The notebooks were verified with Python 3.13 and scikit-learn 1.7.2; generated `models/model_metadata.json` records the exact model-related versions. Dependency ranges permit compatible releases but are not an exact environment lock.

From this repository root:

```bash
python -m venv .venv
```

Activate in Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Or on macOS/Linux:

```bash
source .venv/bin/activate
```

Then install packages and launch Jupyter:

```bash
python -m pip install -r requirements.txt
python -m ipykernel install --user --name tabular-handbook --display-name "Python (Tabular Handbook)"
jupyter lab
```

Select the corresponding Python kernel. Dependencies need installation once; the datasets and core examples need no subsequent network access. NumPy, Pandas, Matplotlib, Seaborn, Scikit-learn, and Joblib are the analysis dependencies; the remaining packages support notebooks and execution. XGBoost, LightGBM, and CatBoost are optional and excluded from the core requirements. Their syntax examples run only if the packages are installed; optional package/version incompatibilities can be skipped without affecting the core workflow.

## Execution

Open the notebooks from the repository root. Run each notebook top to bottom with a fresh kernel (Restart Kernel and Run All). Run **02 → 03 → 04** whenever rebuilding workflow artifacts. Notebook 01 is educational and standalone; Notebook 00 can also be run independently. All paths are relative to the root; launching with a different working directory requires changing back to this folder first.

For batch execution, run these commands **in order** from the root:

```bash
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 00_Python_Fundamentals.ipynb
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 01_Exploratory_Data_Analysis.ipynb
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 02_Data_Preprocessing.ipynb
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 03_Modeling.ipynb
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=300 04_Model_Evaluation.ipynb
```

Outputs are embedded for reading without execution. Re-running notebooks refreshes generated files in `data/processed/` and `models/`; raw data is never overwritten. Preserve a versioned copy of generated model/report files if you need to retain previous experiments. Do not repeatedly revise the model against this known test holdout; use fresh evaluation evidence for future model iterations.

### Artifact handoff

| Artifact | Created by | Purpose |
|---|---|---|
| `data/processed/regression_splits.joblib` | 02 | Pandas X/y splits, store IDs, feature order, and unfitted preprocessing template; authoritative workflow inputs |
| `data/processed/train_fitted_preprocessor.joblib` | 02 | Training-only fitted reference transformer; not reused for cross-validation |
| `data/processed/schema.json` | 02 | Original dtypes/column order, transformed feature names, units, and split sizes |
| `data/processed/training_transformed_preview.csv` | 02 | Human-readable training transformation preview; not the CV input |
| `models/training_candidates.joblib` | 03 | Training-only fitted candidates for validation reporting and interpretation |
| `models/regression_validation_comparison.csv` | 03 | Model-selection evidence: train/validation MAE, validation RMSE/R² |
| `models/final_sales_pipeline.joblib` | 03 | Selected model refitted on development rows, including its fitted preprocessing |
| `models/final_sales_baseline.joblib` | 03 | Development-fitted median baseline |
| `models/model_metadata.json` | 03 | Selection rule, feature order, units, seed, and package versions |
| `models/classification_case.joblib` | 03 | Separate classification splits, fitted models, CV evidence, chosen model, and threshold |
| `models/final_*_test_metrics.csv` | 04 | Final locked-model evaluation reports |

Joblib preserves column names, dtypes, indices, and fitted state. CSV previews do not preserve all of these. Never load serialized files from untrusted sources; Python serialization can execute code. Use the original dependency versions or retrain for a different environment.

## Using this as a syntax reference

Search for `Technique:`, `Quick Syntax Reference`, or the operation you need: `loc`, `merge`, `StandardScaler`, `OneHotEncoder`, `RandomForestRegressor`, `GridSearchCV`, `precision`, or `permutation_importance`. Each major technique explains what, why, when, runnable syntax/example, interpretation, and cautions. Cells reuse earlier setup; copy the relevant imports and input definitions when adapting code. Expected directions and output-reading guidance are written in Markdown; data-specific figures and scores are calculated, not manually fabricated.

## Adapting to another dataset or competition

1. Preserve the new raw file under `data/raw/` and document its provenance, row unit, prediction time, target, and units. Do not assume the competition's unlabeled test file can be used for local scoring.
2. Update the loader, dtype rules, target, feature list, and IDs in 01 and 02 consistently. Exclude target-derived, post-outcome, and identifier leakage.
3. Define the split before exploratory target analysis. Use stratification for classification, grouping for repeated entities, or chronological splits for future prediction. Keep identical row assignments across notebooks.
4. Revise ColumnTransformer selectors and encoding choices. Fit imputation, scaling, learned engineering, and selection inside each model's CV pipeline.
5. Rebuild 02 and 03. Choose baselines, estimators, CV scoring, and validation metrics appropriate to the task. If changing the primary project to classification, change the target handling, estimators, selection rule, and final metrics together; do not reuse regression losses blindly.
6. Lock the choices, refit on permitted development data, and evaluate once in 04. After a major revision informed by test results, the old test is now development evidence; seek a fresh holdout or a rigorous resampling plan.
7. Save schema, versions, full pipelines, and a short decision log. For competition submissions, preserve row IDs and sample-submission order, fit only on allowed labeled development data, and check the event's rules on external data and leakage. Repeated leaderboard tuning can itself overfit.

## Scope and limitations

This is a substantial reference, not a claim that every listed technique belongs in every project. Advanced statistics, deployment services, deep learning, and exhaustive tuning are intentionally outside scope. The small primary dataset is ideal for learning syntax but too limited for strong causal, profitability, or production conclusions. The right next step is often better data and clearer validation, not a more complex model.
