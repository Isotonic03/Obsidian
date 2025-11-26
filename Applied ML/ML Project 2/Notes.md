### **Q1: Statistical Summary and Visualization**

Most features show different means and standard deviations, indicating that the dataset includes measurements on varying scales.  
The attributes exhibit different ranges, and some contain missing values that require preprocessing.  
Histograms reveal that several features are right-skewed, suggesting the presence of higher values in a small portion of the samples.  
The `ammonia` and `is_safe` attributes required conversion from object to numeric types for proper analysis.  
The correlation heatmap indicates that most features have weak to moderate correlations, suggesting that each attribute contributes unique information to the dataset.  
Some features display skewness and possible outliers, indicating that normalization or scaling will be necessary before model training.

---

### **Q2: Relationship Analysis using Pearson Correlation and Scatter Plots**

The Pearson Correlation Coefficient (PCC) analysis shows that most attributes have weak correlations with one another, indicating low multicollinearity.  
The correlation heatmap highlights a few moderate positive or negative relationships between some features, but no extremely strong correlations are observed.  
When examining correlations with the target variable `is_safe`, most features exhibit weak relationships, suggesting that no single feature alone can determine water safety.  
Scatter plots between features and the target show scattered points without clear linear separation, which implies that nonlinear models or combinations of multiple features may be required for accurate classification.  
Overall, the relationships between attributes and the target are mild, reinforcing the need for multivariate analysis during model building.

----
### Q3:

The dataset was divided into three subsets to prepare for model training and evaluation.  
A stratified split was used to ensure that both safe and unsafe samples are proportionally represented across all subsets.  
The training set contains 70% of the data and is used to train the model, the validation set (15%) is used for tuning model parameters and preventing overfitting, and the test set (15%) is reserved for final model performance evaluation.  
This split provides a balanced and reliable foundation for training and assessing the predictive model for water quality classification.

---
### Q4.

Logistic regression:
The logistic regression model performs decently on all three sets, showing that the data is somewhat linearly separable. Increasing `C` (lower regularization) can improve fit but may lead to overfitting. The `lbfgs` solver works well for small-to-medium datasets, and increasing `max_iter` ensures convergence.




Validation Accuracy (Hard Voting): 0.951

---