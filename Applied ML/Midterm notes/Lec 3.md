## RMSE
RMSE measures how far off your predictions are from actual values.

![[Pasted image 20251019163452.png]]

Where:

- m = number of data points
    
- y^(i) = h (x^i) = model’s predicted value
    
- y(i) = Actual value
    

✅ **Smaller RMSE = better model**

- h is our system’s prediction function,also called a hypothesis. 

When the system is given an instance’s feature vector x(i), it outputs a predicted value ŷ(i) = h(x(i)). 


## MAE
Even though the RMSE is generally the preferred performance measure for regression tasks, in some contexts you may prefer to use another function. 

For example, suppose that there are many outlier districts. In that case, you may consider using the mean absolute error (MAE, also called the average absolute deviation)

![[Pasted image 20251019164145.png]]

<span style="color:rgb(0, 176, 80)">MAE = L1 norm = Manhattan norm</span>
- Robust to outliers since it is using absolutes
- Treats all data equally
<span style="color:rgb(0, 176, 80)">RMSE = L2 norm = Euclidean norm</span> 
- Not good for outliers, punishes large errors 
- Good for gaussian/Normal distribution


## What is Correlation?

Correlation measures the strength and direction of a relationship between two numerical variables.

In simple words:

It tells you whether — and how strongly — changes in one variable are associated with changes in another.

⸻

🔁 Example:

Income ($)	House Value ($)
40,000	200,000
50,000	250,000
60,000	300,000

Here, as income increases, house value also increases → strong positive correlation. <span style="color:rgb(0, 176, 80)">+1</span>

If one goes up while the other goes down (e.g., latitude ↑, temperature ↓), that’s a negative correlation. <span style="color:rgb(0, 176, 80)">-1</span>

📊 2. The Correlation Coefficient (Pearson’s r)

The Pearson correlation coefficient (often written as r) is a number between –1 and +1:

Value of r	Meaning	Example
+1	Perfect positive linear correlation	Income ↑ → House value ↑ in perfect proportion
0	No linear correlation	No pattern between shoe size and happiness
–1	Perfect negative linear correlation	Distance from city ↑ → House price ↓ perfectly


🔍 3. Linear vs Nonlinear Relationships

This is key — and what your textbook snippet highlighted:
	•	Correlation measures only linear relationships.
	•	<span style="color:rgb(255, 0, 0)">So r = 0 doesn’t necessarily mean “no relationship”; it means “no straight-line relationship”.</span>



💡 4. What correlation actually represents

Correlation coefficient r measures two things:
	1.	<span style="color:rgb(255, 192, 0)">Direction of the relationship</span> → positive or negative
	2.	<span style="color:rgb(255, 192, 0)">Strength of the relationship</span> → how closely the points align to a straight line

	•	r = +1 → perfect positive linear relationship
	•	r = -1 → perfect negative linear relationship
	•	r = 0 → no linear relationship

⸻

📈 5. Sign vs Magnitude
	•	The sign (positive or negative) only shows direction:
	•	Positive → as X increases, Y increases
	•	Negative → as X increases, Y decreases
	•	The magnitude (absolute value |r|) shows strength of the relationship.

<span style="color:rgb(0, 176, 80)">The closer |r| is to 1, the stronger the linear relationship — regardless of the sign.</span>

Example:
A	+0.4	Positive	Moderate	
B	–0.8	Negative	Strong	 ✅
## Feature scaling

Feature scaling ensures all numeric features have **similar ranges**.  
Why?  
Because many ML algorithms (like linear regression, SVMs, or KNN) perform poorly when one feature (say, income in dollars) has a much larger scale than another (like population ratio).

---

### 🔹 Method 1: **Min–Max Scaling (Normalization)**

Scales all values between **0 and 1**.​
$$ ⁡x' = \frac{x - x_{\min}}{x_{\max} - x_{\min}} $$

![[Pasted image 20251019171526.png]]

✅ Works well, but **very sensitive to outliers** (extreme values), bad if we have outliers

---

### 🔹 Method 2: **Standardization (Z-score Scaling)**

Centers data around **mean = 0**, <span style="color:rgb(0, 176, 80)">**standard deviation </span>= 1**.

$$ x' = \frac{x - \mu}{\sigma} $$

where:

- μ = mean
    
- σ = standard deviation
    

✅ Less affected by outliers, good if we have outliers  
✅ Keeps negative and positive values

###  Why It Matters

If one feature (like income = 100,000) dominates others (like age = 30), the model may **focus too heavily** on the large numbers.  
Scaling prevents this — all features contribute equally.
