[Decision trees visually explained](https://www.youtube.com/watch?v=ZVR2Way4nwQ)

Its a tree of decisions.
- Takes a condition as the main leaf
- When condition is true, move to left
- When condition is false, move to right
- If all the items in a particular category is collected then it is called <span style="color:rgb(255, 192, 0)">leaf</span> 
- if not it will keep continuing the tree depth until maximum depth (Hyperparameter)


![[Pasted image 20251026221315.png]]

A node’s gini attribute measures its impurity: a node is “pure” (<span style="color:rgb(255, 192, 0)">gini=</span>0) if all training instances it applies to belong to the same class.

### Gini Impurity (Formula on Slide 5)

Each node has a measure of **impurity**, which tells how “mixed” the classes are in that node.

$$ G_i = 1 - \sum_{k=1}^{n} p_{i,k}^2 $$​

Where:

- Gi​: Gini impurity for node _i_
    
- pi,k​: proportion of class _k_ in node _i_
    

✅ If a node is **pure** (all data points belong to one class),  
then pi,k=1 for that class → Gi=0

✅ If data is perfectly mixed (50-50), impurity is higher.

So, during training, the tree **splits data** so impurity **keeps decreasing**.


## Estimating probability 

A Decision Tree can also estimate the probability that an instance belongs to a particular class k.

For example, suppose you have found a flower whose petals are 5 cm long and 1.5 cm wide.

The corresponding leaf node is the depth-2 left node, so the Decision Tree should output the following probabilities: 
0% for Iris setosa (0/54),  
90.7% for Iris versicolor (49/54), 
9.3% for Iris virginica (5/54).


## **CART Algorithm**

### 🔹 What is CART?

CART = **Classification and Regression Tree algorithm**

That’s what scikit-learn uses internally.

---

### 🧮 How CART Works

1. Start with all data in one node (the root).
    
2. Try **every feature (k)** and **every possible threshold (tₖ)**.
    
3. Find the (feature, threshold) pair that **best splits the data** into two subsets.
    
    - The “best” split = the one that makes both groups as **pure as possible**.
        
4. Repeat this process **recursively** for each subset until:
    
    - Max depth is reached, or
        
    - No split reduces impurity anymore.
        

✅ **Greedy algorithm** — it doesn’t look ahead, only makes the best local split now.

---

### ⚙️ CART Cost Function (for classification)

For each possible split, CART minimizes:

$$ J(k, t_k) = \frac{m_{\text{left}}}{m} G_{\text{left}} + \frac{m_{\text{right}}}{m} G_{\text{right}} $$​

Where:

- mleft​, mright​: number of samples in left/right subsets
    
- Gleft, Gright​: their Gini impurities
    

✅ Choose the split (k, tₖ) that gives the **lowest cost**.

---

### ⚠️ Note:

Finding the perfect tree (globally optimal) is **NP-complete** (computationally impossible for large datasets).  
So CART uses this greedy (approximate) method.

---

## 🧩 **Slide 11: Gini Impurity vs. Entropy**

Another way to measure impurity = **Entropy**, from information theory.

which measures the average information content of a message: entropy is zero when all messages are identical. 

In Machine Learning, entropy is frequently used as an impurity measure: a set’s entropy is zero when it contains instances of only one class.

$$ H = - \sum_{k} p_k \log_2(p_k) $$

- Entropy = 0 → perfectly pure
    
- Entropy is similar to Gini but penalizes mixed nodes a bit more strongly.

✅ In practice, both work similarly; Gini is faster to compute, so often used.

---

## 🧩 **Slide 12–13: Regularization Hyperparameters**

Decision Trees can easily **overfit**, because they can keep splitting until every leaf has just one sample.

To avoid that, we **regularize** (limit) the tree’s growth.

Common regularization parameters:

- `max_depth` → maximum levels in the tree
    
- `min_samples_split` → minimum samples needed to split a node
    
- `min_samples_leaf` → minimum samples allowed in a leaf
    
- `max_leaf_nodes` → limit number of leaves
    

✅ This helps the model generalize better to new data.

---

## 🧩 **Slide 14–17: Regression with Decision Trees**

When used for **regression**, the logic is the same — but instead of classifying, the model predicts continuous values.

- Use **DecisionTreeRegressor** in scikit-learn.
    
- The splits are chosen to minimize **Mean Squared Error (MSE)** instead of impurity.
    

$$ MSE = \frac{1}{m} \sum (y_i - \hat{y}_i)^2 $$

✅ So at each node, the model finds the split that results in the **lowest average MSE** in both children.

⚠️ Just like classification, regression trees can **overfit**, so we again use regularization parameters.

---

## 🧩 **Slide 18–20: Instability of Decision Trees**

Decision Trees are **unstable** — small changes in training data can lead to **very different trees**.

Example:

- If you remove just one sample from the dataset, the first split might change, and so will the entire structure.
    

This happens because each split depends on earlier ones → any change cascades through the tree.

✅ They also prefer **orthogonal (axis-aligned)** splits, which means they are sensitive to feature rotations (if data is rotated, the splits change).

---

## 🧩 **Slide 21: Fixing Instability**

The solution = **Random Forests** 🌳  
They average predictions from many trees trained on random subsets of data → reduces variance and increases stability.