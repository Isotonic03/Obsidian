
Logistic Regression works only for **binary classification** (two outputs: 0 or 1).  

Softmax Regression extends this to **multi-class classification** (3 or more classes).

### 🧠 What happens conceptually:

When you give an input xxx to the model:

1. It calculates a **score** for each class → sk(x)s_k(x)sk​(x)
    
2. It then converts those scores into **probabilities** using the **Softmax function**
    
sk(x)=θkTxs_k(x) = \theta_k^T xsk​(x)=θkT​x

where:

- sk(x)s_k(x)sk​(x) = score for class _k_
    
- θk\theta_kθk​ = weight vector (each class has its own θ)
    
- All θ’s are stored in one **parameter matrix Θ**
    

✅ So if you have 3 classes → you’ll have 3 separate sets of θ’s (one for each class).

## **The Softmax Function**

Once we have all the scores, we use the **Softmax** (also called “normalized exponential”) function:

$$ {p}_k = \frac{e^{s_k(x)}}{\sum_{j=1}^{K} e^{s_j(x)}} $$​

where:

- esk(x) → exponentiates all scores to make them positive
    
- The denominator normalizes them so all probabilities sum to 1.
    

✅ This gives us a probability distribution across classes:

$$ \hat{p}_1 + \hat{p}_2 + \hat{p}_3 = 1 $$

Each p^​k​ tells us:

> “How likely is this example to belong to class k?”


### Why “Softmax”?

Because it’s a “soft” version of the **argmax** (which picks only the largest score).  
Instead of just picking the max score, Softmax turns all scores into smooth probabilities.


## **Making Predictions**

After Softmax gives probabilities for all classes:

- The model predicts the class with the **highest probability** (or score).
    

$$ \hat{y} = \text{argmax}_k(\hat{p}_k) $$

But:

- It predicts only **one class** per input (i.e., **multiclass**, not **multi-label**).
    
- Use it only when classes are **mutually exclusive** (e.g., flower species).
    

You can’t use it for overlapping categories (e.g., “contains person A” and “contains person B”).

## Optimization

Minimizing the cost function (negative log likelihood), called the cross entropy,

Now, how do we train Softmax Regression?

We want:

> High probability for the correct (target) class  
> Low probability for all other classes.

To achieve that, we use a **cost function** called **Cross-Entropy**.

### Likelihood for one data point:

$$ L = \prod_{k=1}^{K} (\hat{p}_k)^{y_k} $$​

Here:

- yk​ = 1 for the correct class, 0 for all others (one-hot encoding)
    
- p^k = predicted probability for class k
    

For example, if actual label is “Iris Virginica” (class 3):

- y=[0,0,1]
    
- L=p^ 3
    

---

### 🔹 Negative Log Likelihood (Cross Entropy Loss):

To make optimization easier, we take the negative log:

$$ J(\theta) = -\sum_{i=1}^{m}\sum_{k=1}^{K} y_k^{(i)}\log(\hat{p}_k^{(i)})$$

✅ This penalizes wrong confident predictions:

- If the model predicts a low probability for the correct class → big penalty.
    
- If the model is confident and correct → small penalty.


## Cross entropy
refer slide 7

## **Gradient Descent for Cross-Entropy**

We compute the **gradient (derivative)** of the cross-entropy cost with respect to the weights (Θ).

Then, we use **Gradient Descent** (or other optimizers like SGD, Adam, L-BFGS) to update the weights:

$$ \Theta := \Theta - \alpha \frac{\partial J}{\partial \Theta} $$​

✅ This reduces the cost function gradually until it converges (lowest cross-entropy).


## **Example — Iris Flower Classification**

Softmax regression can classify all **3 Iris species** directly:

- Iris Setosa
    
- Iris Versicolor
    
- Iris Virginica
    

In **Scikit-Learn**, you can use:

```python
`from sklearn.linear_model import LogisticRegression model = LogisticRegression(multi_class="multinomial", solver="lbfgs")`
```

- Setting `multi_class="multinomial"` enables **Softmax regression**.
    
- The default `one-vs-rest` is replaced by a true **multi-class model**.
    
- It uses **L2 regularization** by default (to prevent overfitting).  
    You can control this with hyperparameter `C` (smaller `C` → stronger regularization).
    

---

### 🌸 Example output:

You input a flower with:

- Petal length = 5 cm
    
- Petal width = 2 cm
    

Model outputs:

- Iris Virginica (class 2) → 94.2% probability
    
- Iris Versicolor → 5.8% probability
    

✅ The class with the highest probability (Virginica) is chosen.

---

## 🧩 **Slide 11: Decision Boundaries**

Softmax regression creates **linear decision boundaries** (because the logit scores are linear in x).

For the Iris example:

- Each pair of classes has a linear boundary (straight line).
    
- The boundaries intersect at points where all 3 classes have equal probability (≈ 33% each).
    

Even though the probability contours (the “confidence” curves) look curved,  
the actual **decision boundaries** between classes are straight lines.

![[Pasted image 20251026084937.png]]