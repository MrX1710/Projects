# Credit Default Prediction

Binary classification of loan applicants into **default / non-default** using demographic, financial and loan-level features. Three models (Logistic Regression, Random Forest, XGBoost) are compared on a held-out test set, with a focus on how each one handles the class imbalance (about 22% of loans default).

Everything is in one notebook: `credit_risk.ipynb`.

## Results

Test set: 6,482 loans (20% stratified split, 1,418 defaults). Decision threshold: 0.33.

| Model | ROC-AUC | Accuracy | Precision (default) | Recall (default) | F1 (default) |
|---|---|---|---|---|---|
| **XGBoost** | **0.946** | **0.92** | **0.84** | 0.80 | **0.82** |
| Random Forest | 0.928 | 0.89 | 0.73 | **0.81** | 0.77 |
| Logistic Regression | 0.864 | 0.79 | 0.51 | 0.79 | 0.62 |

XGBoost is the best model on every metric except recall, where Random Forest is marginally ahead (0.81 vs 0.80). Logistic Regression catches about as many defaulters as the other two, but only by flagging many good borrowers (precision 0.51).

The threshold is set to 0.33 instead of the default 0.5 so that more defaulters are flagged, trading precision for recall. In credit risk, approving a loan that defaults is usually costlier than rejecting one that would have been repaid.

## Dataset

Public credit risk dataset (`credit_risk_dataset.csv`): 32,581 loans, 12 columns.

| Column | Description |
|---|---|
| `person_age` | Applicant age |
| `person_income` | Annual income |
| `person_home_ownership` | RENT / OWN / MORTGAGE / OTHER |
| `person_emp_length` | Employment length (years) |
| `loan_intent` | Purpose of the loan |
| `loan_grade` | Loan grade (A to G) |
| `loan_amnt` | Loan amount |
| `loan_int_rate` | Interest rate |
| `loan_status` | **Target**: 0 = non-default, 1 = default |
| `loan_percent_income` | Loan amount as a share of income |
| `cb_person_default_on_file` | Previous default on file (Y/N) |
| `cb_person_cred_hist_length` | Credit history length (years) |

## Pipeline

**1. Cleaning**
- Removed 165 duplicate rows.
- Removed rows with impossible values: age above 100 or employment length above 62 years (7 rows; the raw data contains ages up to 144 and employment lengths up to 123 years).
- Missing values: 895 in employment length (2.7%) and 3,116 in interest rate (9.6%), imputed with the median.
- Final dataset: 32,409 loans.

**2. Encoding and feature engineering**
- `Historical_default` mapped to 0/1; the other categorical variables label-encoded.
- Five engineered features: loan-to-income ratio, estimated interest cost, employment-to-age ratio, income per year of age, and a risk exposure score (loan amount x interest rate).

**3. Train/test split and imbalance handling**
- Stratified 80/20 split (`random_state=42`) so both sets keep the same default rate.
- Each model handles the imbalance differently:

| Model | Imbalance strategy |
|---|---|
| Logistic Regression | Standardized features + SMOTE on the training set only (minority raised to 50% of the majority class) |
| Random Forest | `class_weight='balanced'` |
| XGBoost | `scale_pos_weight` = non-default / default ratio in the training set |

**4. Models**
- Logistic Regression (`C=1`, `lbfgs`, `max_iter=2000`)
- Random Forest (100 trees, `min_samples_split=10`, `min_samples_leaf=3`)
- XGBoost (600 trees, `subsample=0.8`)

**5. Evaluation**
- ROC curves and AUC, confusion matrices and classification reports, all on the test set.

## Limitations

This is a learning project on a public dataset, not a production credit model:

- **One split, no cross-validation, no hyperparameter tuning.** The results come from a single train/test split with hand-picked parameters, so small differences between models should not be over-interpreted. The gap between XGBoost and Logistic Regression is large enough to hold up; the gap between XGBoost and Random Forest is smaller.
- **The 0.33 threshold is fixed, not optimized.** 
- **Absence of Cross-Validation**

## How to run

```bash
git clone https://github.com/MrX1710/Projects.git
cd Projects/<this-project-folder>
pip install numpy pandas matplotlib seaborn scikit-learn xgboost imbalanced-learn jupyter
jupyter notebook credit_risk.ipynb
```
make sure to keep `credit_risk_dataset.csv` in the same folder as the notebook.

## stack

Python, pandas, NumPy, scikit-learn, XGBoost, imbalanced-learn (SMOTE), matplotlib, seaborn.