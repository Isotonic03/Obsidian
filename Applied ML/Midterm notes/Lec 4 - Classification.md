## [Regression](https://www.geeksforgeeks.org/machine-learning/regression-in-machine-learning/)

Regression in machine learning refers to a supervised learning technique where the goal is to predict a <span style="color:rgb(255, 192, 0)">continuous numerical value</span> based on one or more independent features


## [Classification](https://www.geeksforgeeks.org/machine-learning/getting-started-with-classification/)

- Classification teaches a machine to sort things into categories. It learns by looking at examples with labels After learning, it can decide which category new items belong to.
- For example a classification model might be trained on dataset of images labeled as either ****dogs**** or ****cats**** and it can be used to predict the class of new and unseen images as dogs or cats based on their features such as color, texture and shape.


## Types of Classification
1. Binary Classification
    This is the simplest kind of classification. In binary classification, the goal is to sort the data into ****two distinct categories****: yes or no, cat or dog, bike or car
2. Multiclass Classification
    the data needs to be sorted into ****more than two categories****
3. Multi label classification 
    multi-label classification single piece of data can belong to ****multiple categories**** at once.
    A movie recommendation system could tag a movie as both ****action**** and ****comedy****.

## Classifiers algorithms

1. ****Linear Classifiers****: Linear classifier models create a linear decision boundary between classes. They are simple and computationally efficient. Some of the linear ****classification**** models are as follows: 

- [Logistic Regression](https://www.geeksforgeeks.org/machine-learning/understanding-logistic-regression/)
- [Support Vector Machines having kernel = 'linear'](https://www.geeksforgeeks.org/machine-learning/support-vector-machine-algorithm/)
- [Single-layer Perceptron](https://www.geeksforgeeks.org/python/single-layer-perceptron-in-tensorflow/)
- [Stochastic Gradient Descent (SGD) Classifier](https://www.geeksforgeeks.org/python/stochastic-gradient-descent-classifier/)

2. ****Non-linear Classifiers****: Non-linear models create a non-linear decision boundary between classes. They can capture more complex relationships between input features and target variable. Some of the non-linear ****classification**** models are as follows:

- [K-Nearest Neighbours](https://www.geeksforgeeks.org/machine-learning/k-nearest-neighbours/)
- [Kernel SVM](https://www.geeksforgeeks.org/machine-learning/major-kernel-functions-in-support-vector-machine-svm/)
- [Naive Bayes](https://www.geeksforgeeks.org/machine-learning/naive-bayes-classifiers/)
- - [Decision Tree Classification](https://www.geeksforgeeks.org/machine-learning/decision-tree/)
- [Ensemble learning classifiers:](https://www.geeksforgeeks.org/machine-learning/ensemble-classifier-data-mining/) 
- [Random Forests,](https://www.geeksforgeeks.org/dsa/random-forest-classifier-using-scikit-learn/) 
- [AdaBoost,](https://www.geeksforgeeks.org/machine-learning/implementing-the-adaboost-algorithm-from-scratch/) 
- [Bagging Classifier,](https://www.geeksforgeeks.org/machine-learning/What-is-Bagging-classifier/)


**Binary / Multi-class / Multi-label** → what kind of _problem_ it is  
**Linear / Nonlinear** → what kind of _model_/algorithm you use to solve it


|Problem Type|Model Type|Example|
|:--|:--|:--|
|**Binary + Linear**|Logistic Regression|Spam (yes/no) using email text frequency|
|**Binary + Nonlinear**|Random Forest|Tumor detection (benign/malignant) using medical scans|
|**Multi-class + Linear**|Linear SVM (One-vs-Rest)|Handwritten digits (0–9)|
|**Multi-class + Nonlinear**|Decision Tree or Neural Network|Image classification|
|**Multi-label + Nonlinear**|Neural Network|Tagging a photo with multiple labels (cat + dog + person)|

## Performance metrics

In regression we have RMSE, MAE, MSE to measure performance

In classification we’re not predicting numbers — we’re predicting _label_

we use **count-based metrics**

| Metric                   | Meaning                                           | Formula                               |
| :----------------------- | :------------------------------------------------ | :------------------------------------ |
| **Accuracy**             | Fraction of correct predictions                   | ( \frac{TP + TN}{TP + TN + FP + FN} ) |
| **Precision**            | Out of predicted positives, how many were correct | ( \frac{TP}{TP + FP} )                |
| **Recall (Sensitivity)** | Out of actual positives, how many were caught     | ( \frac{TP}{TP + FN} )                |
| **F1 Score**             | Balance between Precision & Recall                | ( 2 \times \frac{P \times R}{P + R} ) |
| **Confusion Matrix**     | Counts of TP, TN, FP, FN                          | Table form                            |
| **ROC Curve / AUC**      | Trade-off between true and false positive rates   | AUC close to 1 = good                 |

- Precision focuses on _quality_ of positive predictions.
    
- **Recall** focuses on _quantity_ — how many actual positives you captured.
    

So:

- High precision → few false alarms
    
- High recall → few missed positives

Example:
Both **Precision** and **Recall** measure how well your model identifies **positive** cases —  
but they focus on **different sides** of the problem.

---

### ⚙️ The Confusion Matrix

||**Predicted Positive**|**Predicted Negative**|
|---|---|---|
|**Actual Positive**|✅ **TP** (True Positive)|❌ **FN** (False Negative)|
|**Actual Negative**|❌ **FP** (False Positive)|✅ **TN** (True Negative)|

---

## 🎯 **Precision**

**Definition:**

> Of all the things the model <span style="color:rgb(255, 192, 0)">**predicted as positive**</span>, how many were **actually positive**?

✅ High precision → **few false alarms** (FP low)

---

## 🎯 **Recall**

**Definition:**

> Of all the things that were<span style="color:rgb(255, 192, 0)"> **actually positive**</span>, how many did the model **correctly detect**?

✅ High recall → **few misses** (FN low)

---

## 🧩 **Example Scenario**

Imagine you’re building a model to detect **fraudulent transactions** 💳

|Type|Count|
|---|---|
|Actual Fraud (Positive)|100|
|Actual Legit (Negative)|900|

---

### 🔹 Case 1: Model A — very **cautious**

It only predicts “fraud” when it’s _very sure_.

||Predicted Fraud|Predicted Legit|
|---|---|---|
|**Actual Fraud**|40 (**TP**)|60 (**FN**)|
|**Actual Legit**|10 (**FP**)|890 (**TN**)|

📊

- Precision = 40 / (40 + 10) = **0.80**
    
- Recall = 40 / (40 + 60) = **0.40**
    

✅ Model A is **precise** (few false alarms)  
❌ But it **misses** many frauds.

---

### 🔹 Case 2: Model B — very **sensitive**

It flags anything _suspicious_ as fraud.

||Predicted Fraud|Predicted Legit|
|---|---|---|
|**Actual Fraud**|90 (**TP**)|10 (**FN**)|
|**Actual Legit**|100 (**FP**)|800 (**TN**)|

📊

- Precision = 90 / (90 + 100) = **0.47**
    
- Recall = 90 / (90 + 10) = **0.90**
    

✅ Model B catches most frauds (high recall)  
❌ But many false alarms (low precision)
### Accuracy
Accuracy is not good when you are dealing with skewed datasets (i.e., when some classes are much more frequent than others)

Accuracy=Number of correct predictions​ / Total predictions

### Confusion Matrix 

- The first row of this matrix considers non-5 images (negative)
- The second row considers the images of 5s (positive)

![[Pasted image 20251022191636.png]]

<span style="color:rgb(255, 192, 0)">These 4 numbers are the foundation for **all other performance metrics**.</span>

### Precision 

**Precision** measures how _accurate your positive predictions_ are.

Precision= $$ {Precision} = \frac{TP}{TP + FP} $$​
.

👉 Example:  
If model predicts 100 “5”s, but only 80 are correct:

Precision=80/100=0.8\text{Precision} = 80 / 100 = 0.8Precision=80/100=0.8

So 80% of “5” predictions are correct.

### Sensitive/recall

**Recall** (also called **Sensitivity** or **True Positive Rate**) measures how many of the _actual 5’s_ were detected.

Recall= $$ {Recall} = \frac{TP}{TP + FN} $$​

High recall → few misses.

👉 Example:  
If there are 100 actual “5”s, and model correctly detects 90:

Recall=90/100=0.9\text{Recall} = 90 / 100 = 0.9Recall=90/100=0.9

So it catches 90% of all 5’s.


### F1

Sometimes, you want a **single number** that balances both precision and recall.

That’s the **F1 score** — the _harmonic mean_ of precision and recall.

F1= $$ F1 = 2 \times \frac{Precision \times Recall}{Precision + Recall} $$​

- If precision and recall are both high → F1 is high.
    
- If one of them is low → F1 drops quickly.
    

👉 Example:  
Precision = 0.8, Recall = 0.6  
Then:

F1=2×0.8×0.60.8+0.6=0.6857F1 = 2 \times \frac{0.8 \times 0.6}{0.8 + 0.6} = 0.6857F1=2×0.8+0.60.8×0.6​=0.6857

So F1 penalizes imbalance — both metrics must be good.


<span style="color:rgb(255, 192, 0)">Unfortunately, you can’t have it both ways: increasing precision reduces recall, and vice versa. This is called the precision/recall trade-off</span> 


### ROC and AUC
The **ROC Curve (Receiver Operating Characteristic)** is another way to visualize performance.

It plots:

- **True Positive Rate (Recall)** on the Y-axis
    
- **False Positive Rate (FPR)** on the X-axis

A perfect classifier hugs the **top-left corner** (high recall, low false positives).

One way to compare classifiers is to measure the area under the curve (AUC).

A perfect classifier will have a ROC AUC equal to 1, whereas a purely random classifier will have a ROC AUC equal to 0.5.

- AUC = 1.0 → perfect classifier
    
- AUC = 0.5 → random guessing
    

So the **bigger the area**, the better the classifier.


## Multi-Class Performance metrics (OvR and OvO)

So far, we handled **binary** classification (5 vs not-5).  
Now, MNIST has **10 digits (0–9)** → **multi-class classification**.

### 1️⃣ One-vs-Rest (OvR or One-vs-All)

- Train **10 classifiers** — one for each digit.
    
- Each classifier learns to detect one digit vs all others.
    
- The one with the highest confidence “wins.”
    

### 2️⃣ One-vs-One (OvO)

- Train a classifier for **every pair of digits** (0 vs 1, 0 vs 2, 1 vs 2, etc.).
    
- Total classifiers = 10 × 9 / 2 = 45 models.
    
- Each model votes — the class with most votes is predicted.

✅ OvR → simpler, fewer models.  
✅ OvO → more accurate but heavier computationally.
