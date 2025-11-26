The goal is to find the parameters θ0,θ1,...,θn​ that **minimize the error** between predicted values and true values

two ways to train linear regression models to minimize the erorrs
 1. The **Normal Equation** “closed-form” equation
 2. Gradient Descent (GD)

![[Pasted image 20251019234043.png]]

Can be written as :
![[Pasted image 20251019234407.png]]


## Normal Equation (Closed Eq):

Why do we need normal equation?
We want our model to predict output y^\hat{y}y^​ as close as possible to actual value:

$$ \hat{y} = X\theta $$

Our objective is to find the **best θ** that minimizes the **cost function** (MSE value)

there are two ways to minimize this:

| Method               | How it works                                                                       |
| -------------------- | ---------------------------------------------------------------------------------- |
| **Gradient Descent** | Iteratively moves θ in the direction that reduces cost (slow but scalable).        |
| **Normal Equation**  | Uses linear algebra to compute θ **directly** (fast and exact for small datasets). |

![[Pasted image 20251020065608.png]]

the Normal Equation finds the **exact θ mathematically** by solving for where the cost function’s slope = 0.

In calculus, the minimum of a function occurs where the **derivative = 0**.

In other words — you plug your data (X, y) into this equation, and out comes the “perfect” model weights.

It’s like pressing a button to instantly find the best-fitting line.

Example:
Let’s say you have data points:

|x|y|
|---|---|
|1|2|
|2|3|
![[Pasted image 20251020065734.png]]


### When Normal Equation does not work
Normal Equation may not work if the matrix X^T X is not invertible (i.e., singular)
• if m < n (where n is the number of features) (m is the No. of rows in dataset)
• if some features are redundant

Then use Use **pseudo-inverse** (SVD approach)$$ \hat\theta =  X^+ y$$

Both the Normal Equation and the SVD approach get very slow when the number of features grows large (e.g., 100,000). 

On the positive side, both are linear with regard to the number of instances in the training set (they are O(m)), so they handle large training sets efficiently, provided they can fit in memory.

### Computational Complexity
The computational complexity of inverting such a matrix is typically about O(n2.4) to O(n3), depending on the implementation. 

In other words, if you double the number of features, you multiply the computation time by roughly 22.4 = 5.3 to 23 = 8. 

The SVD approach used by Scikit-Learn’s Linear Regression class is about O(n2). If you double the number of features, you multiply the computation time by roughly 4

### In conclusion
- The **Normal Equation** gives the **exact best-fitting line** by solving where the cost function’s slope = 0.
    
- So instead of “trial and error” (gradient descent), you directly compute the optimal solution.


## Gradient Descent 

Gradient Descent is an iterative optimization algorithm used to minimize a cost function by adjusting model parameters in the direction of the steepest descent of the function’s gradient.

<span style="color:rgb(0, 176, 80)">It is better suited for cases where there are a large number of features or too many training instances to fit in memory</span>

<span style="color:rgb(0, 176, 80)">In simple terms, it finds the optimal values of weights and biases by gradually reducing the error between predicted and actual outputs.</span> 

Process:
1. ****Start at the Top:**** You begin at the top of the hill (this is like starting with random <span style="color:rgb(0, 176, 80)">guesses for the model's parameters</span>).
2. ****Feel the Slope:**** You look around to find out which direction the ground is sloping down. This is like <span style="color:rgb(0, 176, 80)">calculating the gradient</span>, which tells you the steepest way downhill.
3. ****Take a Step Down:**** Move in the direction where the slope is steepest (this is <span style="color:rgb(0, 176, 80)">adjusting the model's parameters</span>). The bigger the slope, the bigger the step you take.
4. ****Repeat:**** You keep repeating the process — feeling the slope and moving downhill — until you ****reach the bottom of the vall****ey (this is when the <span style="color:rgb(0, 176, 80)">model has learned and minimized the error</span>).

we move **against the gradient** to make the cost go down.

Think of hiking:

- The gradient points uphill.
    
- To reach the valley (minimum), you must walk **downhill**, i.e. the **negative** of the gradient.

![[Pasted image 20251020074933.png]]

### When does Gradient Descent stop?

It stops when:

- The gradient becomes almost zero (the slope is flat — bottom of the valley), <span style="color:rgb(0, 176, 80)">Slope =0</span>
    
- Or the cost function stops decreasing significantly between iterations
    

At that point, we’ve found the θ values that minimize MSE —  
meaning our line fits the data as well as possible.

### What happens after the gradient descent converges?

Let’s say you’re training a simple linear regression model:

$$ \hat{y} = \theta_0 + \theta_1 x $$

Gradient Descent starts with random guesses for θ₀ and θ₁, then updates them repeatedly:

Each step moves closer to the point where J(θ) is minimum.

Finally, suppose it converges at:

θ0=1.2, θ1=0.9

✅ That’s the **bottom of the cost bowl** — the lowest MSE you can achieve.

---

#### 🧮 2️⃣ What happens at that point

You now have:

$$ \hat{y} = 1.2 + 0.9x $$

This is your **final learned model** — your **best-fit line**.


<span style="color:rgb(255, 0, 0)">Problem:</span> 

Not all cost functions look like nice, regular bowls. There may be holes, ridges, plateaus, and all sorts of irregular terrains, making convergence to the minimum difficult.  

This is the problem in Batch Gradient descent to solve this we use SGD

Fortunately, the MSE cost function for a Linear Regression model happens to be a convex function, which means that if you pick any two points on the curve, the line segment joining them never crosses the curve. 

This implies that there are no local minima, just one global minimum and Gradient Descent is guaranteed to approach arbitrarily close the global minimum (min point ) (if you wait long enough and if the learning rate is not too high)

![[Pasted image 20251020082130.png]]


## Learning rate hyperparameter

It is a gradient descent's Hyperparameter 

Learning rate is a important hyperparameter in gradient descent that controls how big or small the steps should be when going downwards in gradient for updating models parameters.


If the learning rate is too small, then the algorithm will have to go through many iterations to converge, which will take a long time.

![[Pasted image 20251020075456.png]]



If the learning rate is too high, you might jump across the valley and end up on the other side, possibly even higher up than you were before. This might make the algorithm diverge, with larger and larger values, failing to find a good solution.
![[Pasted image 20251020075513.png]]

## Gradient Descent variations

1. Batch Gradient Descent : <span style="color:rgb(255, 192, 0)">(Full dataset/ iteration)</span> computes gradients using the entire dataset in each iteration.
2. Stochastic Gradient Descent : <span style="color:rgb(255, 192, 0)">(1 dataset / iteration)</span> uses one data point per iteration to compute gradients, making it faster. Picks a random instance in the training set at every step and computes the gradients based only on that single instance
3. Mini batch Gradient Descent: <span style="color:rgb(255, 192, 0)">(mini batch 30-40 data/ iteration)</span> combines batch and SGD by using small batches of data for updates.
   The main <span style="color:rgb(0, 176, 80)">advantage of Minibatch</span> GD over Stochastic GD is that you can get a performance boost from hardware optimization of matrix operations, especially when using GPUs.


which are **all types of Gradient Descent** that differ in <span style="color:rgb(0, 176, 80)">**how much data they use per step**</span> when updating the parameters.

Gradient Descent repeatedly updates the model’s parameters (θ₀, θ₁, etc.) to **reduce the cost function J(θ)**.

How do we calculate this “gradient” — the slope — each time? With these variations we can calculate

### Stochastic Gradient descent:

It is used when <span style="color:rgb(255, 192, 0)">When the cost function is very irregular,</span>

When using Stochastic Gradient Descent, the training instances must be independent and identically distributed (IID) to ensure that the parameters get pulled toward the global optimum,  

A simple way to ensure this is to shuffle the instances during training (e.g., pick each instance randomly, or shuffle the training set at the beginning of each epoch). 

If you do not shuffle the instances—for example, if the instances are sorted by label—then SGD will start by optimizing for one label, then the next, and so on, and it will not settle close to the global minimum

#### Learning schedule
Randomness is good to escape from local optima, but bad because it means that the algorithm can never settle at the minimum. 

The solution to this dilemma is to gradually reduce the learning rate. The steps start out large (which helps make quick progress and escape local minima), then get smaller and smaller, allowing the algorithm to settle at the global minimum (simulated annealing). 

<span style="color:rgb(255, 192, 0)">The function that determines the learning rate at each iteration is called the learning schedule</span>

![[Pasted image 20251020082354.png]]


### What is an Epoch?

An **epoch** means **one complete pass of the entire training dataset** through the learning algorithm.

1 epoch = 1 revision at each iteration in SGD

Imagine you’re learning vocabulary words:

- You have 1000 flashcards (the training data).
    
- **1 epoch** = you’ve gone through all 1000 cards once.
    
- You might need several rounds (epochs) before you remember everything perfectly.



