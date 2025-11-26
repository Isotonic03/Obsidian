## **Slide 1 — Introduction to Machine Learning**
Machine Learning (ML) is a branch of Artificial Intelligence (AI) that teaches computers to **learn from data** instead of being explicitly programmed with rules.

- Traditional programming: Humans write all rules manually.
    
- ML: The computer finds patterns from data and makes predictions or decisions on its own.
    

Example:  
Instead of writing rules to detect spam emails, we give a model thousands of emails labeled as “spam” or “not spam.” It _learns_ the patterns and can classify new emails automatically.

## **Slide 6 — Common Divisions of ML Algorithms**

Machine Learning algorithms are divided mainly by **how they learn** from data:

1. **Supervised Learning** — Learn from labeled data.
    
2. **Unsupervised Learning** — Learn from unlabeled data.
    
3. **Semi-Supervised Learning** — Mix of both labeled and unlabeled data.
    
4. **Reinforcement Learning** — Learn from rewards and punishments through interaction.
    


What are Algorithms?
An **algorithm** is simply a<span style="color:rgb(0, 176, 80)"> **step-by-step set of rules or procedures**</span> a computer follows to solve a problem or make a decision.

In **machine learning**, an algorithm tells the computer <span style="color:rgb(0, 176, 80)">**how to learn patterns**</span> from the data.

Why We Use Algorithms in Supervised Learning??

In **supervised learning**, we have:

- **Input features (X)** — the data we feed the model (e.g., house size, location)
    
- **Output labels (Y)** — the correct answers we want it to learn (e.g., house price)
    

The **goal** of the algorithm is to<span style="color:rgb(0, 176, 80)"> **learn the relationship between X and Y**,</span> so that when new data comes in, it can predict the correct output.

Working:
- **You feed data to the algorithm.**  
    Example:
    
    |Size (sqft)|Price ($)|
    |---|---|
    |1000|200,000|
    |1500|250,000|
    
- **The algorithm finds patterns** — for instance, “larger houses cost more.”
    
- **It creates a model** (like a formula or decision rule) that captures this relationship.
    
- **You test it on new data** to see how well it predicts.


| Algorithm                           | Type                      | What it Does                                                 | Example                                          |
| ----------------------------------- | ------------------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| **Linear Regression**               | Regression                | Finds a straight-line relationship between input and output. | Predicting house prices.                         |
| **Logistic Regression**             | Classification            | Predicts categories (yes/no).                                | Spam or not spam.                                |
| **k-Nearest Neighbors (kNN)**       | Classification/Regression | Compares new data to the closest examples.                   | Predicting movie ratings based on similar users. |
| **Decision Trees / Random Forests** | Classification/Regression | Learn decision rules from data.                              | Predicting loan approval.                        |
| **Support Vector Machines (SVMs)**  | Classification            | Finds the best boundary between classes.                     | Separating images of cats vs dogs.               |
<span style="color:rgb(255, 0, 0)">The algorithm first finds the relationship between X and Y, then it teaches or creates a model (Mathematical equation) and gives it to the machine to predict the value.”</span> 


## **Slides 7–9 — Supervised Learning**

- In **supervised learning**, we train the algorithm using a **training set** that contains both **inputs (features)** and **desired outputs (labels)**.
    
- The algorithm learns the mapping between input and output.

**Example:**  
Predicting house prices — inputs are size, location, and rooms; output (label) is price.

**Common Supervised Algorithms:**

- **k-Nearest Neighbors (kNN):** Predicts based on the closest data points. Looks at the neighbours values and give the average value as the result
    
- **Linear Regression:** Fits a straight line to predict continuous values.
    
- **Logistic Regression:** Used for classification (e.g., spam or not spam).
    
- **Support Vector Machines (SVMs):** Finds best boundaries between classes.
    
- **Decision Trees and Random Forests:** Make decisions based on feature splits (like a flowchart).

## **Slides 10–12 — Unsupervised Learning**

- In **unsupervised learning**, we
- only give **inputs**, not labels.
    
- The system tries to find structure or patterns on its own.

**Examples:**

- Grouping customers with similar buying patterns (clustering).
    
- Reducing large datasets to fewer dimensions for visualization.

**Popular Algorithms:**

- **Clustering:** Groups similar items.
    
    - **K-Means:** Forms “k” groups of similar data.
        
- **Anomaly Detection:** Finds unusual data points.
    
    - **One-Class SVM, Isolation Forest**
        
- **Dimensionality Reduction / Visualization:**
    
    - **PCA (Principal Component Analysis)**
        
    - **LLE (Locally Linear Embedding)**
        
    - **t-SNE (t-Distributed Stochastic Neighbor Embedding)**

##  **Slide 15 — Semi-Supervised Learning**

- Real-world datasets often have **a few labeled** and **many unlabeled** samples.
    
- Example: You may have 1,000 labeled medical images but 100,000 unlabeled ones.
    
- Semi-supervised algorithms use the labeled data to guide learning from unlabeled data.

---

##  **Slide 16 — Reinforcement Learning**

- In reinforcement learning, the model is called an **agent**.
    
- The agent **interacts with an environment**, takes **actions**, and receives **rewards or penalties**.
    
- It learns over time to maximize rewards.
    

**Example:**  
A self-driving car learns to stay on the road by getting rewards for correct turns and penalties for crashes.  
The decision-making strategy it develops is called a **policy**.

## **Slides 17–18 — Batch vs Online Learning**

### **Batch Learning**

- Trained once on the entire dataset.
    
- Cannot learn new data without retraining from scratch.
    
Example: k-Nearest Neighbors (kNN)

This is the most common instance-based algorithm.

1. You feed it all your data (it stores it).
    
2. When a new example arrives:
    
    - It finds the **k** closest stored examples (neighbors).
        
    - It uses their **labels** to make a prediction.
        

### Example:

Training data:

|Size (sqft)|Price ($)|
|---|---|
|1000|200,000|
|1200|220,000|
|1500|250,000|

Now a new house comes (size = 1300).  
The algorithm looks for nearby points (say, 1200 and 1500).  
Then it predicts:

Predicted price=220,000+250,0002 /2 =235,000

✅ No equation, no parameters — just “find similar examples and average them.”
### **Online Learning**

- Learns **incrementally** as new data arrives.
    
- Useful when data comes continuously (like stock prices).
    
- Faster and adapts in real time.

**Example of Model-Based Learning**

Example: “Does money make people happy?”

- Use data from OECD and IMF — GDP per capita vs life satisfaction.
    
- Choose a simple model:
    
    life_satisfaction=θ0+θ1×GDP per capita\text{life\_satisfaction} = \theta_0 + \theta_1 × \text{GDP per capita}life_satisfaction=θ0​+θ1​×GDP per capita
- Train the model (find θ₀ and θ₁ that fit the data best).
    
- Use it to predict satisfaction for countries not in the dataset (e.g., Cyprus).


## **Slide 34 — Hyperparameter Tuning and Model Selection**

- ML models have **hyperparameters** (e.g., number of trees, regularization strength).
    
- Testing many values on the **test set** can cause overfitting to that test data.
    
- Instead, use **holdout validation**:
    
    - Split training data into a smaller training subset and a **validation set**.
        
    - Choose the model that performs best on the validation set.
- Hyperparameters are settings or configuration options that control how a machine learning algorithm learns.
- They are **not learned automatically** from the data — you, the user, set them **before training starts**.

| Algorithm                        | Common Hyperparameters                              | What They Control                                |
| -------------------------------- | --------------------------------------------------- | ------------------------------------------------ |
| **Linear Regression**            | Regularization strength (α or λ)                    | Prevents overfitting by penalizing large weights |
| **k-Nearest Neighbors (kNN)**    | Number of neighbors (k)                             | How many examples to look at when predicting     |
| **Decision Trees**               | Max depth, min samples per leaf                     | Controls how complex the tree can grow           |
| **Random Forest**                | Number of trees, max depth                          | Balances accuracy vs speed                       |
| **SVM (Support Vector Machine)** | C (regularization), kernel type                     | Controls margin size and decision boundary shape |
| **Neural Networks**              | Learning rate, number of layers, batch size, epochs | How fast and how much the network lea            |

Hyperparameters **determine the quality of learning**:

- Too small or simple → **underfitting** (model doesn’t learn enough)
    
- Too large or complex → **overfitting** (model memorizes noise)
    

The right settings help your model **generalize better** to unseen data.

There’s no “one-size-fits-all” — tuning is experimental.  
Here are common methods:

1. **Manual tuning:**  
    Try different values by intuition or experience.  
    Example: try k=3, 5, 7 in kNN and compare accuracy.
    
2. **Grid Search:**  
    Automatically test **all combinations** of parameter values and pick the best one.
    
3. **Random Search:**  
    Randomly sample combinations — faster than testing everything.
    
4. **Cross-Validation:**  
    Split your data into several parts (folds), train and test multiple times — ensures your chosen hyperparameters generalize well.
    
5. **Automated methods (advanced):**  
    Techniques like **Bayesian optimization** or **Optuna** libraries can automatically find near-optimal hyperparameters.

|k|Accuracy on validation data|
|---|---|
|1|80% (overfits — too specific)|
|5|90% (good balance)|
|15|84% (underfits — too simple)|

You’d pick **k = 5**, where the model performs best on unseen data.