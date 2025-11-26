<span style="color:rgb(255, 192, 0)">Linear Models:</span> Normal equation is used

1. **Simple Linear Regression**|One input variable (feature) → one output. Predict salary from years of experience.|
2. **Multiple Linear Regression**|Many input variables → one output. Predict house price from size, age, location.|
3. **Polynomial Regression**|Nonlinear relationships but still linear in parameters (since we add powers as new features). Predict bacterial growth vs nutrient concentration.

Polynomial Regression is **nonlinear in features**, but **linear in parameters**(Theta 1, Theta 2 when changed are linear)

<span style="color:rgb(255, 192, 0)">Non Linear Models:</span> Gradient descent is used

1. **Logistic Regression**: Used for classification (yes/no). Predict if a patient has a disease.|
2. **Exponential Growth Model**: Used for growth or decay. Predict population growth.|
3. **Function Fitting (e.g., sinusoidal)**: Fit curves like sine/cosine for periodic data. Predict seasonal temperature changes.


## Polynomial Regression

Problem: What if data looks curved, not straight?

✅ Solution: Use **Polynomial Regression**.  
You still use a **linear regression algorithm**, but you **add powers of x** as new features.

Example:

$$ y = \theta_0 + \theta_1x + \theta_2x^2$$

Now the line can curve to match nonlinear data.


Note that when there are multiple features, Polynomial Regression is capable of finding relationships between features. 

This is made possible by the fact that Polynomial Features also adds all combinations of features up to the given degree. 11  For example, if there were two features a and b, PolynomialFeatures with degree=3 would not only add the features a2, a3, b2, and b3, but also the combinations ab, a2b, and ab


## Regularization

Underfitting: When the curves reaches a plateau

- Both training and validation errors are high.
    
- Model is too simple (e.g., straight line for curved data).

Overfitting:  

- Training error is very low, but validation error is high.
    
- Model is too complex (memorized training data).


### 1️⃣ What training and validation errors represent

| Type of error        | Computed on                   | Meaning                                          |
| -------------------- | ----------------------------- | ------------------------------------------------ |
| **Training error**   | Data the model was trained on | How well the model learned the training examples |
| **Validation error** | New (unseen) data             | How well the model generalizes to new situations |

So:

- **Low training error** = model fits training data well.
    
- **Low validation error** = model performs well on unseen data (good generalization).
    
- **High validation error** = model fails to generalize (overfits).
    

---

### 🧮 2️⃣ Usual situations and what they mean

|Case|Training Error|Validation Error|What’s happening|
|---|---|---|---|
|① Both high|✅ Underfitting|Model too simple (e.g., straight line for curved data)||
|② Training low, Validation high|⚠️ Overfitting|Model memorized training data, poor generalization||
|③ Both low|🎯 Good fit|Model complexity just right||
|④ Training high, Validation low|⚠️ Rare/Unusual case|Something odd (usually data leak or training issue)||


A good way to reduce overfitting is to regularize the model (i.e., to constrain it): the fewer degrees of freedom it has, the harder it will be for it to overfit the data

**Polynomial degree controls this tradeoff:**

- Low degree → underfits.
    
- High degree → overfits.

For Polynimial regression: regularize a polynomial model is to <span style="color:rgb(255, 192, 0)">reduce the number of polynomial degrees.</span> 
For Linear Model: regularization is typically achieved by <span style="color:rgb(255, 192, 0)">constraining the weights</span> of the model


We will now look at t<span style="color:rgb(255, 192, 0)">hree different ways to constrain the weights</span>: 
1. Ridge Regression
2. Lasso Regression
3. Elastic Net

### Ridge Regression (L2 regularization)

Adds a **penalty term** to the cost function: Alpha

$$ J(θ)=MSE+α∑θj^2 $$​

(where α controls how strong the penalty is)

This discourages large weight values.

Effects:

- If α is large → weights shrink → simpler (flatter) model.
    
- If α=0 → becomes plain linear regression.

### Lasso Regression (L1 Regrularization)

Cost function:

$$ J(θ)=MSE+α∑​∣θj​∣ $$

Differences from Ridge:

- Uses **absolute values**, not squares.
    
- Can shrink some θ’s **exactly to 0** — effectively **removing unimportant features**.
    

So Lasso = **automatic feature selection**.