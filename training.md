# SR-BH 2020 Derived Dataset - Classical Machine Learning Training Specification

## Objective

Create a new Jupyter Notebook named:

```text
model_training.ipynb
```

The notebook must train and evaluate classical machine learning models on the derived SR-BH 2020 dataset.

Mandatory models:

```text
Random Forest
SVM
Logistic Regression
```

---

## Dataset Format

Expected CSV columns:

```text
url
parameter
request_type
label
```

Label meanings:

```text
0 -> Normal
1 -> SQL Injection
3 -> OS Command Injection
4 -> Code Injection
```

---

## Important Rule About Training Features

Do **not** train the models directly with the raw columns:

```text
url
parameter
request_type
```

These columns are raw input columns.

They must only be used to derive useful numerical/text-based features.

---

## Request Type Rule

The dataset is expected to contain only POST requests.

Therefore:

```text
request_type
```

must **not** be used as a model training feature.

It should only be checked for validation purposes.

In the notebook:

* Display unique values of `request_type`
* Confirm that all records are POST
* If unexpected request types exist, report them
* Do not include `request_type` in `X`

---

# Features To Use For Model Training

The final training matrix must be created from  like that:

```text
1. Handcrafted numerical features
```

---

## 1. Handcrafted Numerical Features

Use the following handcrafted features in model training:

```text
url_length
parameter_length
parameter_count
parameter_special_char_count
parameter_digit_count
parameter_alpha_count
url_query_delimiter_count
url_entropy
parameter_entropy
sql_keyword_count
shell_keyword_count
code_keyword_count
```

These features are derived from:

```text
url
parameter
```

but the raw `url` and raw `parameter` columns themselves must not be directly passed to the model.

---


---

## Final Feature Matrix

The final model input must be:

```text
X_final = handcrafted numerical features
```

Do not include:

```text
label
url
parameter
request_type
```

directly inside `X_final`.

`label` must only be used as the target variable:

```text
y = label
```

---

# Preprocessing Steps

Before splitting:

1. Load CSV with pandas.
2. Normalize column names if necessary.
3. Remove exact duplicate rows.
4. Check missing values.
5. Fill missing `url` and `parameter` values with empty strings if needed.
6. Confirm request_type contains only POST.
7. Create handcrafted numerical features.

---

# Dataset Splitting

Split the dataset into:

```text
70% training
15% validation
15% test
```

Requirements:

* Use stratified splitting.
* Use `random_state=42`.
* Preserve label distribution in all splits.

Suggested approach:

```text
First split:
70% train
30% temp

Second split:
15% validation
15% test from temp
```

---

# Models To Train

Train these mandatory models:

## Logistic Regression

Use:

```python
LogisticRegression(
    max_iter=5000,
    class_weight="balanced",
    random_state=42
)
```

## SVM

Use:

```python
LinearSVC(
    class_weight="balanced",
    random_state=42
)
```

## Random Forest

Use:

```python
RandomForestClassifier(
    n_estimators=300,
    class_weight="balanced",
    random_state=42,
    n_jobs=-1
)
```

---

# Scaling Rule

Apply scaling only to handcrafted numerical features.

Important:

* Fit scaler only on training data.
* Transform validation and test data with the same scaler.
* Do not scale TF-IDF features manually.

Use:

```text
StandardScaler
```

For Logistic Regression and SVM, combine:

```text
scaled handcrafted features + TF-IDF features
```

For Random Forest, scaling is not required, but using the same prepared matrix is acceptable for consistency.

---

# Validation Evaluation

Evaluate all models on the validation set.

Calculate:

```text
Accuracy
Macro Precision
Macro Recall
Macro F1
Weighted Precision
Weighted Recall
Weighted F1
```

Create a model comparison table.

Select the best model based mainly on:

```text
Macro F1
```

because the dataset may be imbalanced.

---

# Test Evaluation

After selecting the best model using validation results, evaluate it once on the test set.

Calculate:

```text
Accuracy
Precision
Recall
F1-Score
Confusion Matrix
```

Report metrics:

```text
overall
per class
```

Per-class labels:

```text
Normal
SQL Injection
OS Command Injection
Code Injection
```

---

# Confusion Matrix

Generate:

```text
Raw confusion matrix
Normalized confusion matrix
```

Use readable class names on axes.

Save figures into:

```text
models_output/
```

---



---

# Final Report Section

At the end of the notebook, answer:

1. Which model performed best?
2. Which class had the highest F1-score?
3. Which class had the lowest F1-score?
4. Did handcrafted features help?
5. Did TF-IDF features help?
6. Are there signs of overfitting?
7. What should be improved in the dataset or features?

---

# Notebook Requirements

The notebook must:

* Run from top to bottom without errors.
* Use clear Markdown explanations.
* Keep each step in separate readable cells.
* Use pandas, numpy, scikit-learn, matplotlib and seaborn.
* Save plots into `models_output/`.
* Avoid data leakage.
* Avoid using raw `url`, `parameter`, or `request_type` directly as model features.

```
```
