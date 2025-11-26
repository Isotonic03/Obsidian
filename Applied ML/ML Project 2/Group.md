### 🔹 **Data Understanding and Preprocessing**

**Q1:** Why did you only take 480 rows?

> Because each rock subtype has 16 token samples, and we only use the first 480 samples (30 subtypes × 16 = 480) as specified in the project instructions. Rows 481–720 are not used to maintain consistent sampling per subtype.

**Q2:** What do columns 3–14 represent?

> Columns 3–14 are 11 continuous-valued features extracted from the rock images (for example, texture, mineral presence, or intensity values). These features describe physical or spectral properties used to classify rock type.

**Q3:** Why did you convert everything to numeric and fill NaNs with 0?

> Because the dataset contained possible missing or empty cells after cleaning. We replaced empty strings with NaN and converted all values to numeric to ensure model compatibility. Missing values were filled with 0 since 0 indicates feature absence (which aligns with the rock feature encoding in the dataset).

**Q4:** Why did you scale your data (StandardScaler)?

> We used `StandardScaler` because features had different ranges (some near 0, some up to 1). Algorithms like Logistic Regression and SVM are distance-based and sensitive to scale, so scaling ensures that all features contribute equally.

---

### 🔹 **Exploratory Data Analysis (EDA)**

**Q5:** From `.describe()`, what did you observe?

> Most features have a mean below 0.2, and median (50%) = 0 for many features — indicating strong right-skewness and sparsity (many zeros). This suggests that most rocks don’t exhibit all features equally, so certain features may be present only in specific rock types.

**Q6:** What does the correlation heatmap tell us?

> The heatmap shows that most features have **low correlation** (<0.4), meaning they are relatively independent. However, a few moderate correlations suggest some features may co-occur (e.g., features 7 and 10). This independence is good for models like logistic regression.

**Q7:** Why did you plot scatter plots between X and Y?

> To see if individual features visually separate rock types. Scatter plots help identify if certain features (like feature 7) are higher for igneous rocks or if there is overlap between classes — which helps explain classification difficulty.

---

### 🔹 **Model Training and Parameter Tuning**

**Q8:** Why did you choose Logistic Regression, SVM, and Random Forest?

- **Logistic Regression:** Strong linear baseline and interpretable.
    
- **SVM:** Handles non-linear relationships with kernels and margin-based classification.
    
- **Random Forest:** Captures complex feature interactions and gives feature importance.
    

Using all three covers both **linear and non-linear** models and allows ensemble comparison.

**Q9:** What does parameter `C` in Logistic Regression and SVM control?

> `C` is the regularization parameter.

- Low `C` → stronger regularization (simpler model).
    
- High `C` → less regularization (more flexible model).  
    In our case, `C=1` balanced underfitting and overfitting best.
    

**Q10:** What did you observe from the validation accuracies for different C values?

> For Logistic Regression: best at `C=1` (Validation = 63.3%). Too small (`C=0.1`) or large (`C=10`) both slightly reduced accuracy, confirming that moderate regularization works best.

---

### 🔹 **Model Evaluation and Interpretation**

**Q11:** Why are precision, recall, and F1 important here?

> Since we have three classes (Igneous, Metamorphic, Sedimentary), precision tells how accurate each class’s predictions are, recall shows how well each rock type was identified, and F1 balances both.  
> This helps assess if the model is biased toward one rock class.

**Q12:** What do your results show about the model performance?

> Logistic Regression, SVM, and Random Forest all achieved around **67–70% test accuracy**, meaning the dataset has moderate class overlap.  
> Performance consistency between train, validation, and test indicates **no overfitting**.

**Q13:** Why is Random Forest perfect on training but worse on test?

> That’s overfitting — the Random Forest memorized training patterns (100% train accuracy) but generalizes less to new data (≈67% test accuracy).  
> We could reduce overfitting by increasing `min_samples_leaf` or reducing `max_depth`.

---

### 🔹 **Ensemble Model**

**Q14:** Why did you create an ensemble?

> To combine the strengths of the three models — Logistic Regression’s linear boundary, SVM’s margin optimization, and Random Forest’s feature interaction learning. The ensemble (soft voting) improved accuracy to 70% on test data.

**Q15:** Why “soft” voting and not “hard”?

> Soft voting uses class probabilities, allowing more nuanced combination. Hard voting just counts votes and can ignore model confidence.

---

### 🔹 **Feature Importance**

**Q16:** How do you interpret the Random Forest feature importance graph?

> Feature 7 had the highest importance (~0.18), suggesting it strongly influences rock type classification. Features 10 and 13 followed, while 5, 6, 11, and 12 had low importance, meaning they contribute less to discrimination.

---

### 🔹 **Human vs Model Correlation**

**Q17:** What does the correlation between human accuracy and model probability tell us?

> The correlation coefficients (r ≈ 0.02–0.04) show very weak positive correlation — meaning the model’s confidence doesn’t strongly align with human judgment. Humans and models may use different cues for identifying rocks.

---

### 🔹 **General Conceptual Questions**

**Q18:** What would you improve next?

> Try feature selection or PCA to reduce noise, or try a deeper ensemble (e.g., Gradient Boosting or XGBoost). Also, analyze class imbalance or use SMOTE for balancing.

**Q19:** Why might class 3 (Sedimentary) have lower recall?

> Likely due to overlapping features with class 2 (Metamorphic) or fewer distinguishing patterns. This could be improved with non-linear kernels or additional domain-specific features.