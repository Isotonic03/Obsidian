### Imports:

```python
`import pandas as pd`
```

- `pandas` is a **Python library for data manipulation and analysis**.
- Lets you work with data in tables (called DataFrames)
- `pd.read_csv('file.csv')` - Load your happiness data
- `df.head()` - See first few rows
- `df.describe()` - Get statistics (mean, std, min, max)
- `df['column_name']` - Access specific columns

```python
import numpy as np
```

- `numpy` is a **numerical computing library** in Python.
-  Much faster than regular Python for calculations
- Provides **efficient array operations** and mathematical functions.
- Convert DataFrames to arrays for ML (`X = df.values`)
- Perform mathematical operations like mean, standard deviation, or RMSE (`np.sqrt(np.mean(...))`)
- Work with linear algebra operations (important for Linear Regression, Normal Equation)

```python
import matplotlib.pyplot as plt
```
- `pyplot` module (imported as `plt`) provides **functions to create graphs** like line plots, histograms, scatter plots.

```python
import seaborn as sns
```
- `seaborn` is a **high-level statistical plotting library** built on top of matplotlib.
- Makes **visualizations easier and more beautiful**, especially for data analysis.


##### Model Selection tools:

```
from sklearn.model_selection import train_test_split
```
- Uses from .... import because need specific class from sklearn package.
- sklearn(package) -> model_selection(sub folder) -> train_test_split(specific class)
- Splits your data into separate sets for training and test sets

```
from sklearn.model_selection import cross_val_score
```
- Quick perform cross-validation and get scores to Evaluate model performance across multiple folds easily

```
from sklearn.model_selection import KFold
```
- Creates the fold splits for K-fold cross-validation
- Dividing your study material into 4 sections, testing on each while studying the other 3

##### Regression Models:

```python
from sklearn.linear_model import LinearRegression
```
- Regular linear regression using Normal Equation (closed-form solution)
- **Pros:** Fast, exact solution **Cons:** Doesn't work well with huge datasets, no regularization

```python
from sklearn.linear_model import SGDRegressor
```
- Linear regression using **Stochastic Gradient Descent** (iterative approach)
- Instead of calculating the answer directly (like LinearRegression), it takes small steps towards the answer, adjusting based on errors
- **How SGD works:**

1. Start with random weights
2. Make prediction
3. Calculate error
4. Update weights slightly in direction that reduces error
5. Repeat until convergence

```python
from sklearn.linear_model import Ridge
```
- Linear regression with **L2 regularization**
- Adds penalty proportional to **square** of weights
- Shrinks all weights, but doesn't make any exactly zero
- **When to use:** When you have **multicollinearity** (correlated features)
- Without Ridge: weights = [10, 0.1, 8, 0.05, 12] With Ridge:    weights = [5, 0.05, 4, 0.02, 6]  (all shrunk)

```python
from sklearn.linear_model import Lasso
```
- Linear regression with **L1 regularization**
- Adds penalty proportional to **absolute value** of weights
- Can shrink some weights **exactly to zero** = **feature selection**!
- **When to use:** When you suspect some features are **irrelevant** and want automatic feature selection
- Without Lasso: weights = [10, 0.1, 8, 0.05, 12] With Lasso:    weights = [8, 0, 6, 0, 10]  (some are exactly 0!)

```python
from sklearn.linear_model import ElasticNet
```
- Combines **both L1 and L2** regularization
- **Parameters:**
    `alpha`: Overall regularization strength
    `l1_ratio`: To Balance between L1 and L2
    - `l1_ratio=1.0` → Pure Lasso
    - `l1_ratio=0.0` → Pure Ridge
    - `l1_ratio=0.5` → 50-50 mix
- When you want benefits of **both** Ridge and Lasso (common choice!)

##### Data Preprocessing:

```python
from sklearn.preprocessing import PolynomialFeatures
```
- Creates polynomial and interaction features
- Instead of just having `x`, you create `x`, `x²`, `x³` to fit curves

```python
from sklearn.preprocessing import StandardScaler
```
- Standardizes features to have mean=0, std=1 
- **Why you need it:** **CRITICAL for SGD!** SGD performs poorly without scaling
- **CRITICAL RULE:**'
    **FIT** scaler on training data only
    **TRANSFORM** validation and test using the same scaler
    Never fit on test data (data leakage!)

##### Pipeline:

```python
from sklearn.pipeline import Pipeline
```
Chains preprocessing and model training steps 
**Why you need it:** Keeps code clean, prevents errors

Without pipeline (error-prone):
```python
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
model = Ridge(alpha=0.1)
model.fit(X_train_scaled, y_train)
X_test_scaled = scaler.transform(X_test)
y_pred = model.predict(X_test_scaled)
```
With pipeline (cleaner):
```python
pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('model', Ridge(alpha=0.1))
])
pipeline.fit(X_train, y_train)
y_pred = pipeline.predict(X_test)  # Automatically scales!
```

##### Evaluation metrics:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt
```
- Calculates MSE between predictions and actual values **Why you need it:** To calculate RMSE (your main metric!)
- Square root function **Why you need it:** To convert MSE → RMSE

`Need closed-form solution?`
`├─ YES → LinearRegression`
`└─ NO → Continue`

`Using SGD?`
`├─ Need feature selection?` 
`│  └─ YES → Lasso (L1)`
`├─ Have multicollinearity?`
`│  └─ YES → Ridge (L2)`
`├─ Want both benefits?`
`│  └─ YES → ElasticNet`
`└─ No regularization → SGDRegressor(penalty='none')`

`Need polynomial features?`
`└─ YES → PolynomialFeatures + any model above`




### What are Folds in cross validation?

- “Multiple folds” means **repeating the training/testing process several times on different parts of the dataset** to get a more accurate picture of how well your model will perform on unseen data.
When you train and test a machine learning model, you usually **split** your dataset into **Training data** and Test (or validation) data.
But…  
If you do only **one split**, your results might depend on _how the data was split_.  
You might be lucky (easy data in train/test) or unlucky (harder cases in test).

So to make the evaluation **fairer and more reliable**, we use **cross-validation** — which divides data into **multiple folds**. Fold 1 | Fold 2 | Fold 3 | Fold 4

|Round|Training Folds|Testing Fold|
|---|---|---|
|1|Folds 2, 3, 4|Fold 1|
|2|Folds 1, 3, 4|Fold 2|
|3|Folds 1, 2, 4|Fold 3|
|4|Folds 1, 2, 3|Fold 4|

Each round, a **different fold becomes the test set**, and the remaining folds are used for training.

cv=4 is 4 folds

```python
scores = cross_val_score(model, X, y, cv=4, scoring='neg_mean_squared_error')
```

So, if your 4-fold cross-validation gives RMSE scores like:

`[0.42, 0.40, 0.45, 0.41]`

→ Average RMSE = (0.42 + 0.40 + 0.45 + 0.41) / 4 = **0.42**


cross_val_score:
- Automatically performs cross-validation and returns RSME scores

Kfold:
- Just splits data into folds — you handle training and evaluation manually


