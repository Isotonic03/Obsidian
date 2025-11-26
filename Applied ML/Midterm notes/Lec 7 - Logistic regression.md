Logistic Regression (also called **Logit Regression**) is used for **binary classification**, i.e. when your output can only be **two categories**:  
✅ Example:

- Email → spam (1) or not spam (0)
    
- Tumor → malignant (1) or benign (0)

It doesn’t actually “regress” (predict continuous values) — instead, it **estimates the probability** that something belongs to a class.


How prediction works:

1. The model estimates p=P(y=1∣x)p = P(y=1|x)p=P(y=1∣x) → the probability that the instance belongs to the **positive class**.
    
2. If p≥0.5p ≥ 0.5p≥0.5 → predict **1 (positive)**  
    If p<0.5p < 0.5p<0.5 → predict **0 (negative)**
    

<span style="color:rgb(255, 192, 0)">So it’s a **binary classifier** built on regression math.</span> 

## Sigmoid Function

Just like Linear Regression, Logistic Regression computes:

$$ t=θ0+θ1x1+θ2x2+...+θnxn$$

But instead of returning t directly (which can be any number from −∞ to +∞),  
we pass it through a **sigmoid (logistic) function**:

$$ \sigma(t) = \frac{1}{1 + e^{-t}} $$​

✅ This transforms any number into a probability between **0 and 1**.


### 🧠 Why sigmoid?

Because probabilities must be between 0 and 1, and sigmoid perfectly squashes all values into that range.

It looks like an **S-shaped curve**, and the threshold (0.5) becomes your decision boundary.

![[Pasted image 20251026071350.png]]


## **The “Logit” Function** or Log odds

The linear score $$ t = X^Tθ$$ is called the **logit** or **log-odds**.

Why? Because if $$ p=σ(t)$$, then the **inverse** is:

$$ {logit}(p) = \log\left(\frac{p}{1-p}\right) $$

That’s the **logarithm of the odds ratio** between the positive and negative classes.

✅ Intuition:

- If p = 0.5 → odds = 1 → logit = 0
    
- If p = 0.9 → odds = 9 → logit = 2.2
    
- If p = 0.1 → odds = 1/9 → logit = −2.2

So, Logistic Regression is actually **linear regression applied to the log-odds** of the probability.

## **Training and Cost Function**

The goal of training is to find the θ’s (weights) that make:

- Probabilities high for **true class = 1**
    
- Probabilities low for **true class = 0**
    

 The cost for one example:

![[Pasted image 20251026072712.png]]

✅ Why this formula?

- If y=1 but p^\hat{p}p^​ ≈ 0 → log(0) = −∞ → huge cost (bad)
    
- If y=1 and p^\hat{p}p^​ ≈ 1 → log(1)=0 → low cost (good)
    
- If y=0 and p^\hat{p}p^​ ≈ 1 → log(0) = −∞ → huge cost (bad)
    
- If y=0 and p^\hat{p}p^​ ≈ 0 → log(1)=0 → low cost (good)
    

So this formula **punishes wrong confident predictions heavily**.


## Log-Loss / Cross-Entropy Loss - performance metric

To train over all m examples, we average the costs:

![[Pasted image 20251026073011.png]]

✅ This is called **Log Loss** or **Binary Cross-Entropy Loss**.

Goal: minimize J(θ) → find θ that gives smallest average cost.


In Linear regression -> The model predicts **continuous values** (e.g., price, temperature).
So we measure how _far_ predictions are from the real values.
That’s why we use **error-based metrics**: RMSE, MSE, MAE

In Logistic regression -> Here, the model predicts **probabilities**, not continuous numbers.  
So we need a metric that measures **how close our predicted probabilities** are to the actual 0/1 labels.
We can’t use MSE or RMSE, because probabilities behave differently:

- Predictions are between 0 and 1.
    
- The relationship between error and probability is not linear.
    
So **log loss punishes confident wrong predictions very strongly.**
✅ That’s where **Log Loss (Cross-Entropy Loss)** comes in.


👉 **Log Loss is both:**

1. The **training cost function** (used to find the best weights θ)
    
2. A **performance metric** (to evaluate how well the classifier predicts probabilities)
    

So during training, the algorithm **minimizes log loss** (like minimizing MSE in regression).

After training, you can report **log loss value** as the model’s performance measure.

Exmaple:
|True y|Predicted probability|Log Loss contribution|

|   |   |   |
|---|---|---|
|1|0.9|−log(0.9) = 0.105|

|   |   |   |
|---|---|---|
|1|0.1|−log(0.1) = 2.30|

|   |   |   |
|---|---|---|
|0|0.9|−log(1−0.9) = 2.30|

|   |   |   |
|---|---|---|
|0|0.1|−log(1−0.1) = 0.105|
<span style="color:rgb(255, 192, 0)">Average log loss</span> ≈ 1.20 → smaller = better.


## **Optimization (Training)**

We can’t use the **Normal Equation** here (no closed-form solution).  
So we use **Gradient Descent** to minimize the log loss.

Since the cost function is **convex**, gradient descent is guaranteed to reach the global minimum (if the learning rate α is set correctly).

### Gradient formula:
![[Pasted image 20251026075324.png]]


✅ This is the same form as linear regression’s gradient —  
the only difference is that hθ(x) = sigmoid(Xθ) instead of a straight line.

Once you have the gradient vector containing all the partial derivatives, you can use it in the Batch Gradient Descent algorithm


## **Decision Boundaries**

We can visualize logistic regression with the **Iris dataset**.

- The dataset has petal and sepal dimensions for 3 species:
    
    - Iris Setosa
        
    - Iris Versicolor
        
    - Iris Virginica
        

We’ll build a binary classifier:

> Detect **Iris Virginica** (y=1) vs. others (y=0)

### Single Feature Case

If we use only **petal width** as x:

- The model learns a logistic curve (S-shaped) mapping petal width → probability of being Virginica.
    
- The threshold (e.g., 0.5) defines the **decision boundary**.
    

### Two Features Case

With two features (say petal length & width), the decision boundary becomes a **straight line** in 2D space (a plane in 3D).

✅ Logistic Regression always produces **linear decision boundaries** (because it’s linear in θ).

---

## 🧩 **Regularization**

Just like linear regression, logistic regression can **overfit** if features are too many or large.

So we add regularization:

- **L1 (Lasso)** → adds penalty $$ λ∑∣θj​∣$$
    
- **L2 (Ridge)** → adds penalty $$ λ∑θj2$$​
    

✅ These prevent overfitting by keeping θ values small.  
Scikit-learn’s LogisticRegression model uses **L2 regularization by default**.


## Overall process of Logistic regression:
1. Compute the Linear Combination (Logit Function) -> t. This is **not yet a probability**, just a raw linear score.
2. Apply the Sigmoid (Logistic) Function. p = sigmoid x t
   - p^\hat{p}p^​ represents the model’s **predicted probability** that y=1y=1y=1.
    If p^≥0.5 → predict **class 1**
    If p^<0.5 → predict **class 0**
    This step converts a continuous score into a probability for classification.
    
3. Define the Cost Function (Log Loss). We can’t use MSE like in linear regression, because the sigmoid curve is **nonlinear** and would make optimization difficult.
   So we use the **Log Loss (Cross-Entropy Loss):**
   Purpose: Punishes confident wrong predictions heavily. Smooth, convex function → perfect for optimization.
4. Minimize the Cost (Find Best Weights)
    To find the best weights (θ\thetaθ), we minimize J(θ)J(\theta)J(θ) using **Gradient Descent**:
5. Make Predictions
   Once trained, we use the final weights to predict for new data:
   Compute logit: t=XTθt = X^T\thetat=XTθ
   Apply sigmoid: p^=σ(t)\hat{p} = \sigma(t)p^​=σ(t)
   Apply threshold:
    
    - If p^≥0.5\hat{p} \ge 0.5p^​≥0.5 → predict 1
        
    - Else → predict 0
    p^\hat{p}p^​ is the probability; the final prediction is the class label.

6. Evaluate Model
   After training, we check how well it performs using: Accuracy,Precision / Recall / F1,  ROC / AUC, Log Loss (as performance metric)
   ✅ This tells us how confident and correct the model is.
