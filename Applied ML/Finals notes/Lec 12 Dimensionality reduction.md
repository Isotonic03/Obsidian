I'll provide a detailed explanation following each slide exactly as presented in the lecture, using the terminology from the book and slides.

---

## **Slide 3: Why Dimensionality Reduction?**

**The Problem:** Many Machine Learning problems involve **thousands or even millions of features** for each training instance. Let me explain what this means:

- **Training instance**: A single example in your dataset (one row)
- **Features**: The attributes/columns describing each instance

**Three Main Issues:**

1. **Training is slow**: When you have many features, every calculation (matrix multiplication, gradient computation, distance calculation) involves more operations. If you have 10,000 features instead of 100, your algorithms can be 100x slower or more.
    
2. **Harder to find a good solution**: This relates to the curse of dimensionality (coming up). With too many dimensions, finding optimal parameters becomes exponentially harder. The search space is enormous.
    
3. **Intractability**: Some problems are literally **intractable** (impossible to solve in reasonable time) with high dimensions but become **tractable** (solvable) after dimensionality reduction.
    

**Data Visualization Use Case:**

- You cannot visualize 1000-dimensional data
- Reducing dimensions down to **two (or three)** makes it possible to plot a **condensed view**
- This helps you understand patterns, clusters, and outliers
- Example: MNIST has 784 dimensions (28×28 pixels), but we can reduce it to 2D for visualization

---

## **Slide 5-7: The Curse of Dimensionality**

This is one of the most important concepts in the lecture.

### **The Thought Experiment**

The slide asks you to imagine:

- A **200-dimensional ellipsoid** bent in a **1,000-dimensional space**
- A **4D cube** unfolded in **3D**

These are hard to visualize! That's the point - high-dimensional spaces are counterintuitive.

### **The Distance Problem**

Let's understand this carefully with **unit hypercubes** (cubes where each dimension goes from 0 to 1):

**In a unit square (2D):**

- Pick two random points
- Average distance between them: **≈ 0.52**

**In a unit cube (3D):**

- Pick two random points
- Average distance: **≈ 0.66**

**In a 1,000,000-dimensional hypercube:**

- Pick two random points
- Average distance: **≈ 408.25**!

**The Key Insight:** "There's plenty of space in high dimensions"

### **What This Means for Machine Learning**

**Sparsity Problem:**

- **High-dimensional datasets are at risk of being very sparse**
- **Sparse** means: training instances are spread out with lots of empty space between them
- Most training instances are **likely to be far away from each other**

**Prediction Problem:**

- A **new instance** (test point) will likely be **far away from any training instance**
- This is bad because many algorithms (like k-NN, SVM) rely on nearby training examples
- If the nearest training example is far away, predictions become unreliable

### **Can We Just Add More Data?**

**The Exponential Problem (Slide 7):**

The book and slides make a critical point:

> "In practice, the number of training instances required to reach a given density grows **exponentially** with the number of dimensions." (Directly prpotional)

**Concrete Example:**

- With just **100 features** (much less than MNIST's 784)
- To get training instances within **0.1** of each other on average
- Assuming uniform distribution across all dimensions
- You would need **more training instances than atoms in the observable universe**!

**Why exponential?**

- To maintain density in 1D: need n points
- In 2D: need n² points
- In 3D: need n³ points
- In d dimensions: need n^d points

This is why the curse is so severe - you can't just "get more data" to solve it.

---

## **Slide 8-12: Main Approaches - Projection**

### **The Core Observation**

The slide states:

> "In most real-world problems, training instances are **not spread out uniformly across all dimensions**"

**Two key patterns:**

1. **Many features are almost constant**: They barely change across instances, so they carry little information
2. **Others are highly correlated**: If feature A goes up, feature B always goes up proportionally. You don't need both.

**The Result:**

> "All training instances lie **within (or close to) a much lower-dimensional subspace** of the high-dimensional space."

### **What is a Subspace?**

- In a 3D space, a 2D **subspace** could be a plane
- In a 100D space, a 10D subspace is a 10-dimensional "plane" embedded in 100D
- The key: your data doesn't use all available dimensions

### **The Projection Method (Slide 9-10)**

**Slide 9 shows:** A 3D dataset lying close to a 2D subspace (the gray plane)

**Slide 10 shows:** The projection process:

1. Identify the lower-dimensional subspace (the plane)
2. Project every training instance **perpendicularly** onto this subspace
3. Use coordinates within the subspace as your new features
4. Result: 2D dataset instead of 3D

**Perpendicular projection** means: drop a perpendicular line from each point to the plane, like dropping a ball straight down.

### **When Projection Fails (Slide 11-12)**

**The Swiss Roll Dataset:**

This is a **famous toy dataset** mentioned explicitly in the book.

**What it is:**

- A 2D manifold (flat sheet) rolled up in 3D space
- Think: a fruit roll-up or rolled paper

**Why projection fails:**

- The subspace **may twist and turn**
- If you project onto a 2D plane, you **squash** the data
- Points that were far apart on the roll become close after projection
- This **destroys the structure** of the data

**Slide 12 shows two approaches:**

- **Left (bad):** Squashing by projecting onto a plane - points collapse together
- **Right (good):** Unrolling the Swiss roll - preserves distances along the surface

This motivates **manifold learning** methods.

----


---

## **Slide 13-16: Manifold Learning**

### **What is a Manifold? (Slide 13)**

The formal definition from the slides:

> "A **2D manifold** is a 2D shape that can be bent and twisted in a higher-dimensional space."

**More generally:**

> "A **d-dimensional manifold** is a part of an n-dimensional space (where d < n) that **locally resembles a d-dimensional hyperplane**."

**Key phrase: "locally resembles"**

- **Locally**: If you zoom in close enough
- **Resembles a hyperplane**: Looks flat (like how Earth looks flat when you're standing on it)

**Swiss Roll Example:**

- d = 2 (it's a 2D surface)
- n = 3 (it lives in 3D space)
- Locally resembles a 2D plane (if you look at a small patch, it's flat)
- But rolled in the third dimension (globally it's curved)

### **Manifold Learning Algorithms (Slide 14)**

The definition:

> "Many dimensionality reduction algorithms work by **modeling the manifold** on which the training instances lie; this is called **Manifold Learning**."

**The Manifold Assumption:**

> "Manifold learning assumes that most real-world high-dimensional datasets lie close to a much lower-dimensional manifold."

### **MNIST Example - The Manifold Assumption**

The slides walk through why MNIST is a manifold:

**Observations about handwritten digits:**

1. "They are made of **connected lines**" - not random pixels
2. "The borders are **white**" - constraint on edge pixels
3. "They are **more or less centered**" - spatial constraint

**The Key Point:**

> "If you randomly generated images, only a **ridiculously tiny fraction** of them would look like handwritten digits."

**What this means:**

- MNIST has 28×28 = 784 dimensions
- But the space of "valid digit images" is much smaller
- Valid digits live on a **lower-dimensional manifold** within the 784D space
- Most of the 784D space is "empty" (no valid digits there)

Therefore: "MNIST dataset can be **squeezed into a lower dimensional manifold**"

### **The Implicit Assumption (Slide 15)**

The slides make an important additional point:

> "The manifold assumption is often accompanied by another **implicit assumption**: that the task at hand (e.g., classification or regression) will be **simpler if expressed in the lower-dimensional space** of the manifold."

**Why this matters:**

- Not only is the data lower-dimensional
- But classification/regression boundaries are simpler there
- Example: A curved decision boundary in high-D might be linear in the manifold space

### **When This Assumption Fails (Slide 16)**

**Critical warning:**

> "However, this implicit assumption does **not always hold**"

The slide shows an example where:

- Original high-dimensional space: simple decision boundary (classes are separated)
- After dimensionality reduction: complex decision boundary (classes are mixed up)

**Lesson:** Dimensionality reduction can sometimes make your task **harder**, not easier. Always validate that it helps for your specific problem.

---

## **Slide 18-24: Principal Component Analysis (PCA)**

### **Introduction (Slide 18)**

> "Principal Component Analysis (PCA) is by **far the most popular** dimensionality reduction algorithm."

**Two-step process:**

1. "First it identifies the **hyperplane that lies closest to the data**"
2. "Then it **projects the data onto it**"

**What is a hyperplane?**

- In 3D: a hyperplane is a 2D plane
- In n-D: a hyperplane is an (n-1)-D space
- Generally: any lower-dimensional flat subspace

### **Preserving the Variance (Slides 19-21)**

**The Central Question (Slide 19):**

> "Before you can project the training set onto a lower-dimensional hyperplane, you first need to **choose the right hyperplane**."

**Slide 20 shows:** Different projection options (solid line vs dotted line)

**The Selection Criterion (Slide 21):**

> "It seems reasonable to select **the axis that preserves the maximum amount of variance**, as it will most likely **lose less information** than the other projections."

**Why variance?**

- **Variance** measures how spread out the data is
- High variance = data points are far apart = contains information
- Low variance = data points clustered together = lost information

**Alternative justification:**

> "Another way to justify this choice is that it is **the axis that minimizes the mean squared distance** between the original dataset and its projection onto that axis."

**Two equivalent ways to think about PCA:**

1. **Maximize variance** in the projection
2. **Minimize reconstruction error** (distance from original to projection)

### **Principal Components (Slides 22-24)**

**First Principal Component (PC1):**

> "PCA identifies the axis that accounts for the **largest amount of variance** in the training set."

**Second Principal Component (PC2):**

> "It also finds a second axis, **orthogonal to the first one**, that accounts for the largest amount of **remaining variance**."

**Key term: Orthogonal**

- Means perpendicular (at 90 degrees)
- In high dimensions: no correlation between components
- Slide 23 explicitly shows: "principal components are **orthogonal**"

**Higher Dimensions (Slide 24):**

> "If it were a higher-dimensional dataset, PCA would also find a third axis, orthogonal to both previous axes, and a fourth, a fifth, and so on—**as many axes as the number of dimensions** in the dataset."

**Important:** You can find up to n principal components for n-dimensional data, but you typically keep only the first d (where d << n).

---

## **Slide 25-27: Finding Principal Components & Projecting**

### **Singular Value Decomposition (SVD) - Slide 25**

**The Question:**

> "So how can you find the principal components of a training set?"

**The Answer:**

> "There is a **standard matrix factorization technique** called **Singular Value Decomposition (SVD)** that can decompose the training set matrix **X** into the matrix multiplication of three matrices **U Σ V^T**"

**The Decomposition:**

```
X = U Σ V^T
```

Where:

- **X**: Your training set matrix (m instances × n features)
- **U**: Left singular vectors (m × m matrix)
- **Σ** (Sigma): Singular values (m × n diagonal matrix)
- **V**: Right singular vectors (n × n matrix)

**The Key Point:**

> "**V** contains the **unit vectors** that define all the principal components that we are looking for"

**Unit vectors:** Vectors with length 1, representing pure directions

### **SVD Example (Slide 26)**

The slide shows a visual decomposition from Wikipedia:

- Original matrix broken into three component matrices
- This is a standard linear algebra technique
- Libraries like NumPy and scikit-learn have efficient SVD implementations

### **Projecting Down to d Dimensions (Slide 27)**

**The Process:**

> "Once you have identified all the principal components, you can **reduce the dimensionality** of the dataset down to **d dimensions** by projecting it onto the **hyperplane defined by the first d principal components**."

**The Formula:**

**W_d** is defined as: "a matrix containing the **first d columns of V**"

Then your reduced dataset is:

```
X_d-proj = X × W_d
```

**Why first d columns?**

- Principal components are ordered by variance explained
- PC1 explains the most, PC2 second most, etc.
- Keeping first d components keeps maximum variance

**The Guarantee:**

> "Selecting this hyperplane ensures that the projection will **preserve as much variance as possible**."

This is optimal - no other d-dimensional projection preserves more variance.

---

## **Slide 28-29: PCA in Scikit-Learn**

### **Implementation (Slide 28)**

**The Code:**

```python
from sklearn.decomposition import PCA

pca = PCA(n_components=2)
X_2D = pca.fit_transform(X)
```

**Important note from slides:**

> "Note that it **automatically takes care of centering the data**"

**Centering the data** means: subtracting the mean from each feature. This is required for PCA to work correctly.

**After Fitting:**

> "After fitting the PCA transformer to the dataset, its **components_** attribute holds the **transpose of W_d**"

Example:

> "e.g., the unit vector that defines the first principal component is equal to **pca.components_.T[:, 0]**"

This gives you access to the actual principal component directions.

### **Explained Variance Ratio (Slide 29)**

**The Attribute:**

> "Another useful piece of information is the **explained variance ratio** of each principal component, available via the **explained_variance_ratio_** variable."

**What It Tells You:**

> "The ratio indicates the **proportion of the dataset's variance** that lies along each principal component."

**Example Output:**

```python
>>> pca.explained_variance_ratio_
array([0.84248607, 0.14631839])
```

**Interpretation:**

> "This output tells you that **84.2% of the dataset's variance** lies along the first PC, and **14.6%** lies along the second PC"

**What this means:**

- You reduced from many dimensions to just 2
- But you kept 84.2% + 14.6% = **98.8% of the variance**
- Only lost 1.2% of information!
- This is why PCA is so powerful

**Interactive Resource:** The slide provides a Colab link to explore PCA interactively.

---

## **Slide 30-31: Choosing Dimensions & Compression**

### **Choosing the Right Number of Dimensions (Slide 30)**

**One approach:**

> "Plot the **explained variance as a function of the number of dimensions**."

This creates what's called a **scree plot** or **elbow plot**.

**How to read it:**

- X-axis: Number of dimensions
- Y-axis: Cumulative explained variance
- Look for where the curve flattens out

**Example with MNIST:** The slide shows that:

> "Reducing the dimensionality down to about **100 dimensions** wouldn't lose too much explained variance."

**What "too much" means:**

- Started with 784 dimensions
- 100 dimensions might preserve 95%+ of variance
- You decide the threshold based on your application

**Common thresholds:**

- 90%, 95%, or 99% variance preserved
- Or: whatever gives acceptable performance on your task

### **PCA for Compression (Slide 31)**

**The Benefit:**

> "After dimensionality reduction, the training set takes up **much less space**."

**MNIST Example:**

The slide gives a concrete example:

> "Let's apply PCA to the MNIST dataset while **preserving 95% of its variance**, this will mean going from **784 features to 154 features**."

**Compression Ratio:**

- Original: 784 features per image
- Compressed: 154 features per image
- **Ratio:** 154/784 ≈ 19.6% of original size
- **Compression:** ~5:1 (saving ~80% space)
- **Information preserved:** 95%

**How to do this in code:**

```python
pca = PCA(n_components=0.95)  # Preserve 95% variance
X_reduced = pca.fit_transform(X_mnist)
```

Scikit-learn automatically determines that 154 components are needed.

**Use Cases for Compression:**

1. **Storage:** Save disk space for large datasets
2. **Speed:** Faster training with fewer features
3. **Memory:** Fit larger datasets in RAM
4. **Transmission:** Send data faster over networks

---

## **Slide 32: Kernel PCA**

**The Concept:**

> "It turns out that the **same trick we used with SVMs** can be applied to PCA, making it possible to perform **complex nonlinear projections** for dimensionality reduction. This is called **Kernel PCA (kPCA)**."

### **Understanding the "Same Trick"**

**Recall from SVMs:**

- SVMs can use the **kernel trick**
- This implicitly maps data to a higher-dimensional space
- Where it becomes linearly separable
- Without actually computing the high-dimensional coordinates

**Applied to PCA:**

- Regular PCA finds linear projections
- Kernel PCA uses the kernel trick
- Performs PCA in a high-dimensional feature space
- Can capture **nonlinear structure** in the original space

**Common Kernels:**

- **Linear kernel:** Regular PCA
- **RBF (Gaussian) kernel:** Captures local structures
- **Polynomial kernel:** Captures polynomial relationships
- **Sigmoid kernel:** Captures sigmoid patterns

**When to Use Kernel PCA:**

- Your data has **nonlinear structure**
- Regular PCA doesn't separate classes well
- You need more flexible dimensionality reduction
- You're willing to accept slower computation

**Trade-off:**

- **Advantage:** Can capture complex, nonlinear patterns
- **Disadvantage:** Slower than regular PCA, harder to interpret

---

## **Slides 33-36: Locally Linear Embedding (LLE)**

### **Introduction (Slide 33)**

**What is LLE?**

> "Locally Linear Embedding (LLE) is another **powerful nonlinear dimensionality reduction (NLDR) technique**."

**Key Distinction:**

> "It is a **Manifold Learning technique** that **does not rely on projections**, like PCA."

This is fundamentally different! PCA projects onto axes, LLE preserves neighborhoods.

**How LLE Works (High Level):**

The slide gives a two-step process:

1. "First **measuring how each training instance linearly relates to its closest neighbors**"
    
2. "Then **looking for a low-dimensional representation** of the training set where these **local relationships are best preserved**"
    

**Key concept:** **Local relationships**

- Not global structure (like PCA)
- Each point's relationship to its neighbors
- Think: preserve the "neighborhood vibe"

### **Visual Intuition (Slide 34)**

Slide 34 shows the LLE process visually:

- Start with data on a manifold (curved surface)
- Each point has local neighbors
- Map to lower dimensions while keeping neighborhoods intact
- The manifold "unfolds" naturally

### **The LLE Algorithm - Step 1 (Slide 35)**

**Finding Local Linear Relationships:**

> "For each training instance **x^(i)** the algorithm identifies its **k closest neighbors** and then tries to **reconstruct x^(i) as a linear function** of these neighbors."

**What "reconstruct as a linear function" means:**

Express x^(i) as a weighted sum of its neighbors:

```
x^(i) ≈ w_i,1 × x^(neighbor1) + w_i,2 × x^(neighbor2) + ... + w_i,k × x^(neighbork)
```

**The Optimization Problem:**

> "More specifically, it finds the weights **w_i,j** such that the **squared distance** between [x^(i) and its reconstruction] is as small as possible:"

Minimize:

```
||x^(i) - Σ_j w_i,j × x^(j)||²
```

Where:

- The sum is over the k nearest neighbors
- This is done for each instance i
- Weights must sum to 1: Σ_j w_i,j = 1

**What the weights represent:**

- How much each neighbor "contributes" to reconstructing the point
- These weights capture the **local geometry**
- A close neighbor gets higher weight
- These weights are **intrinsic to the manifold**

### **The LLE Algorithm - Step 2 (Slide 36)**

**Mapping to Low Dimensions:**

> "The second step is to **map the training instances into a d-dimensional space** (where d < n) **while preserving these local relationships** as much as possible."

**The New Optimization:**

> "If **z^(i)** is the **image of x^(i)** in this d-dimensional space, then we want the squared distance between [z^(i) and its reconstruction from the low-dimensional neighbors] to be as small as possible."

Minimize:

```
||z^(i) - Σ_j w_i,j × z^(j)||²
```

**The Key Difference:**

> "This looks very similar to the first step, but instead of **keeping the instances fixed and finding the optimal weights**, we are doing the **reverse: keeping the weights fixed and finding the optimal position** of the instances' images in the low-dimensional space."

**Step 1 recap:** Fix positions x^(i), find weights w_i,j  
**Step 2 recap:** Fix weights w_i,j, find positions z^(i)

**Why This Works:**

The weights encode the manifold's geometry. By preserving these weights in the low-dimensional space, we preserve the manifold's structure.

**Intuitive Analogy:**

- You're in a neighborhood (high-D manifold)
- Note your position relative to your neighbors (weights)
- Everyone moves to a new city (low-D space)
- Try to recreate the same relative positions (preserve weights)
- The neighborhood structure is maintained

---

## **Slide 37: LLE Computational Complexity**

**Scikit-Learn Implementation:**

> "Scikit-Learn's LLE implementation has the following computational complexity:"

**Three Stages:**

1. **Finding k nearest neighbors:**
    
    - Complexity: **O(m log(m) n log(k))**
    - Where:
        - m = number of instances
        - n = number of features
        - k = number of neighbors
2. **Optimizing the weights:**
    
    - Complexity: **O(mnk³)**
    - The k³ term comes from solving local linear systems
3. **Constructing low-dimensional representations:**
    
    - Complexity: **O(dm²)**
    - Where d = target dimensions

**The Scalability Problem:**

> "Unfortunately, the **m² in the last term** makes this algorithm **scale poorly to very large datasets**."

**What this means:**

- If you double the dataset size (m → 2m)
- The last step becomes 4× slower (2m)² = 4m²
- This is **quadratic scaling**

**Comparison to PCA:**

- PCA is much faster: roughly O(mn²) or O(n³)
- For large m (many instances), PCA is preferred
- LLE works best for moderate-sized datasets with complex manifold structure

---

## **Slides 38-40: Other Dimensionality Reduction Techniques**

The slides briefly mention several other important techniques. Let me explain each:

### **Random Projections (Slide 38)**

> "As its name suggests, projects the data to a lower-dimensional space using a **random linear projection**."

**Why This Works:**

> "This may sound crazy, but it turns out that such a random projection is actually **very likely to preserve distances well**."

This is based on the **Johnson-Lindenstrauss lemma** - a mathematical result stating that points in high-dimensional space can be projected into lower dimensions while approximately preserving distances, with high probability, using random projections.

**Advantages:**

- Extremely fast
- Simple to implement
- No training needed

**Disadvantages:**

- Less precise than PCA
- Doesn't find "meaningful" directions
- Results vary (it's random)

### **Multidimensional Scaling (MDS) (Slide 38)**

> "**Reduces dimensionality while trying to preserve the distances between the instances**."

**How it works:**

- You have a distance matrix: distance between every pair of points
- MDS finds low-dimensional coordinates that best match these distances
- Like reconstructing a map from a table of distances between cities

**Types:**

- **Classical MDS:** Similar to PCA
- **Metric MDS:** Preserves exact distances
- **Non-metric MDS:** Preserves distance rankings

### **Isomap (Slide 38)**

> "**Creates a graph by connecting each instance to its nearest neighbors**, then reduces dimensionality while trying to preserve the **geodesic distances** between the instances"

**Key term: Geodesic distance**

- The distance along the manifold surface
- Not the straight-line Euclidean distance
- Like: distance walking on Earth's surface vs. distance through Earth's core

**How it works:**

1. Build a **k-nearest neighbors graph**
2. Compute **shortest paths** in the graph (geodesic distances)
3. Use MDS to preserve these geodesic distances

**When to use:** When your manifold is curved and Euclidean distances are misleading

### **t-SNE (Slide 39)**

> "**t-Distributed Stochastic Neighbor Embedding (t-SNE): Reduces dimensionality while trying to keep similar instances close and dissimilar instances apart**."

**Key Properties:**

- **Excellent for visualization** (especially 2D/3D)
- Preserves local structure very well
- Creates beautiful, interpretable plots
- Very popular in exploratory data analysis

**Important Warnings:**

- **Slow** for large datasets
- **Stochastic:** Different runs give different results
- **Distances can be misleading:** Only local neighborhoods matter
- **Not for general dimensionality reduction:** Use mainly for visualization

**Parameters:**

- **Perplexity:** Roughly how many neighbors to consider (typically 5-50)
- Different perplexities can give very different results

### **Linear Discriminant Analysis (LDA) (Slide 39)**

> "**Classification algorithm**, but during training it learns the **most discriminative axes between the classes**, and these axes can then be used to **define a hyperplane onto which to project the data**."

**Key Differences from PCA:**

- **Supervised:** Uses class labels (PCA is unsupervised)
- **Goal:** Maximize class separation (PCA maximizes variance)
- **Discriminative:** Finds directions that best separate classes

**How it works:**

1. Compute the mean of each class
2. Find directions that:
    - Maximize **between-class variance** (classes far apart)
    - Minimize **within-class variance** (classes tight)

**When to use:**

- You have labeled data
- You want dimensionality reduction for classification
- You want to visualize class separation

**Slide 39 shows:** A helpful visualization from botpenguin.com illustrating how LDA finds the optimal projection axis for class separation.

### **Visualization of Many Techniques (Slide 40)**

Slide 40 shows a comparison of various techniques applied to the same dataset:

- Different techniques preserve different aspects
- Some are better for certain data structures
- Visual comparison helps you understand each method's strengths

---

## **Slide 41: Additional Resources - Manifolds in Neuroscience**

The slides mention advanced applications:

> "Additional resources (optional): manifolds in neuroscience"

**Two Papers Cited:**

1. **Nature paper:** https://www.nature.com/articles/s41586-021-03652-7
2. **OpenReview paper:** https://openreview.net/pdf?id=5W9so5v0OU

**Why This Matters:**

Manifold learning is not just a machine learning technique - it's being used to understand:

- How the brain represents information
- Neural population dynamics
- How neural activity lies on low-dimensional manifolds
- Cognitive processes

This shows that dimensionality reduction has applications far beyond ML, including computational neuroscience.

---

## **Key Terminology Summary**

Let me consolidate all the important terms used in the slides:

**Core Concepts:**

- **Training instance:** A single data point/example
- **Features:** Dimensions/attributes of data
- **Curse of dimensionality:** Problems that arise in high dimensions
- **Sparse:** Data points far apart with empty space
- **Variance:** Measure of data spread
- **Subspace:** Lower-dimensional space within higher-dimensional space
- **Hyperplane:** Generalized flat surface in n dimensions

**Projection Terms:**

- **Projection:** Mapping to lower dimensions
- **Perpendicular projection:** Shortest distance to subspace
- **Preserving variance:** Keeping data spread

**Manifold Terms:**

- **Manifold:** Surface that locally looks flat
- **d-dimensional manifold:** d-dimensional surface in higher space
- **Locally resembles:** Looks like when zoomed in
- **Geodesic distance:** Distance along manifold surface
- **Manifold learning:** Methods that model manifolds

**PCA Terms:**

- **Principal component:** Direction of maximum variance
- **Orthogonal:** Perpendicular/uncorrelated
- **Singular Value Decomposition (SVD):** Matrix factorization technique
- **Explained variance ratio:** Proportion of variance captured
- **Unit vector:** Direction vector with length 1
- **Centering:** Subtracting mean from features

**LLE Terms:**

- **Local relationships:** How points relate to neighbors
- **k nearest neighbors:** k closest points
- **Weights:** Coefficients in linear combination
- **Reconstruction:** Approximating point from neighbors
- **Image (of a point):** The point's position in new space

**Other Terms:**

- **Nonlinear dimensionality reduction (NLDR):** Methods for curved structures
- **Kernel trick:** Implicit mapping to higher dimensions
- **Discriminative:** Focused on separating classes
- **Supervised vs unsupervised:** With or without labels
- **Tractable vs intractable:** Solvable vs unsolvable

---

## **Exam Preparation Tips**

Based on this lecture, here's what you should know:

**Conceptual Understanding:**

1. Explain the curse of dimensionality with the distance example
2. Differentiate between projection and manifold learning
3. Explain when projection fails (Swiss roll)
4. Understand manifold assumption and its implicit assumption
5. Know when each method (PCA, Kernel PCA, LLE) is appropriate

**PCA Details:**

1. How PCA finds principal components (maximize variance OR minimize reconstruction error)
2. Principal components are orthogonal
3. SVD decomposes X = UΣV^T, where V contains PCs
4. How to choose number of dimensions (explained variance ratio)
5. PCA for compression (MNIST example: 784 → 154 for 95% variance)

**LLE Details:**

1. Two-step process: find weights, then find positions
2. Preserves local relationships, not global variance
3. Computational complexity, especially the O(dm²) term
4. Difference from PCA (local vs global)

**Comparisons:**

1. PCA vs LLE: linear vs nonlinear, global vs local, fast vs slow
2. Regular PCA vs Kernel PCA: linear vs nonlinear
3. PCA vs LDA: unsupervised vs supervised

Would you like me to create practice problems or go deeper into any specific concept?