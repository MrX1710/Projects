# Adult Income Prediction

A beginner machine learning project for a kaggle competition that predicts whether a person earns **more than $50K per year** based on census data (the Adult / Census Income dataset). It is a binary classification problem.
(this is an old project that i didn't publish back then)

Everything is in one notebook: `Adult_Income.ipynb`.

## Dataset

| File | Rows | Description |
|---|---|---|
| `train1.csv` | 39,073 | Training data with the `income` label |
| `test.csv` | 9,769 | Data to predict, no label |
| `sample_submission.csv` | - | Submission format |

**Target:** `income` (`<=50K` or `>50K`)

**Features (14 + id):** age, workclass, fnlwgt, education, education-num, marital-status, occupation, relationship, race, sex, capital-gain, capital-loss, hours-per-week, native-country.

The classes are imbalanced: in the validation split, about 76% of people are `<=50K` and 24% are `>50K`. This is why accuracy alone is not enough and the `>50K` recall matters.

## Workflow

1. **Load and inspect** the data (shape, types, missing values).
2. **Clean**
   - Dropped the `id` column.
   - `workclass` and `occupation` had ~770 missing values each, filled with `"Unknown"`.
   - `native-country` had 212 missing values, filled with the most frequent value (mode of the training set).
3. **Exploration**
   - Histograms and skewness/kurtosis for numeric columns. `capital-gain` (skew ≈ 11.8) and `capital-loss` (skew ≈ 4.6) are extremely skewed.
   - Count plots for categorical columns.
   - Correlation heatmap of numeric features: no strong correlation between features.
4. **Feature engineering**
   - Grouped `native-country` into regions (North America, Latin America, Europe, Asia, Middle East, Unknown, Other).
   - Grouped `education` into levels (Low, High-School, Some-College, Associate, Bachelors, Graduate).
   - Added age flags: `young_age` (<25), `YoungOld_age` (25-50), `exp_age` (35-55), `retirement_age` (>65).
   - Dropped the original `native-country` and `education` columns.
5. **Preprocessing**
   - One-hot encoding with `pd.get_dummies`, then aligned the train and test columns.
   - `StandardScaler` on the numeric columns.
6. **Split:** 80% train / 20% validation, stratified, `random_state=42`.
7. **Models:** Decision Tree, Random Forest, CatBoost, and a soft-voting ensemble of CatBoost + Random Forest.

## Results (validation set, 7,815 rows)

| Model | Accuracy | `>50K` precision | `>50K` recall | `>50K` F1 |
|---|---|---|---|---|
| Decision Tree | 0.84 | 0.78 | 0.47 | 0.59 |
| Random Forest | 0.86 | 0.79 | 0.56 | 0.65 |
| **CatBoost** | **0.87** | 0.79 | **0.65** | **0.71** |

Random Forest PR-AUC: **0.788**.

**Best validated model: CatBoost.** All models are good at spotting `<=50K` (recall 0.94-0.96) and weaker at catching `>50K` people, which is expected with imbalanced classes.

## Model settings

- **Decision Tree:** `max_depth=5`, `min_samples_split=20`, `min_samples_leaf=10`, `max_features=5`
- **Random Forest:** 250 trees, `max_depth=15`, `min_samples_split=20`, `min_samples_leaf=15`, `max_features=5`
- **CatBoost:** 1000 iterations, `learning_rate=0.05`, `depth=6`, Logloss

## Known limitations
- The scaler is fit on the full training data before the train/validation split, which leaks a small amount of validation information into preprocessing. The effect is small, but it is not the correct order.
- No hyperparameter tuning (grid or random search) and no cross-validation. `cross_val_score` is imported but not used.
- The decision threshold is the default 0.5. Lowering it would raise `>50K` recall at the cost of precision.
- `fnlwgt` (a census sampling weight) is kept as a normal feature, and the heavy skew of `capital-gain` and `capital-loss` is not treated.
- The notebook outputs for the decision tree show `max_depth=15` while the code says `5`, so the saved output is from an older run.
- The country and education grouping dictionaries were generated with AI assistance.
- Imports for `LabelEncoder`, `OneHotEncoder`, `RobustScaler`, `Pipeline` are unused.

## How to run

```bash
git clone https://github.com/MrX1710/Projects.git
cd Projects/<this-project-folder>
pip install pandas numpy matplotlib seaborn scikit-learn catboost jupyter
jupyter notebook credit_risk.ipynb
```

## Stack

Python, pandas, NumPy, Matplotlib, Seaborn, scikit-learn, CatBoost
