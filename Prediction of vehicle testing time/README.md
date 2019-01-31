## Prediction of vehicle testing time   

### Table of contents
* [Introduction](#introduction)
* [Technologies](#technologies)
* [Algorithms](#algorithms)
* [Approach](#approach)
* [Summary](#summary)

### Introduction
Predicting the test time for Mercedes benz cars after manufacturing was the main objective of this project. Using XGBoost, we achieved an R squared value of ~55% for the prediction model.

Codes:  
[Mercedes-Benz Greener Manufacturing .ipynb](https://github.com/akhilesh-reddy/Data-Science-Mini-projects/blob/master/Prediction%20of%20vehicle%20testing%20time/Mercedes-Benz%20Greener%20Manufacturing%20.ipynb) has the code for the analysis

### Technologies  
Project is created with:  
* Python 3.6.5
 
### Algorithms
* Lasso Regression  
* Ridge Regression  
* MLP perceptron  
* XGBoost  

### Approach:  

Steps followed to create the association rules  

1.Data pre processing 
 * Read in the data and look at its shape
 * Which is the dependent variable?
 * Does our data have missing values?  
 * Remove Correlated variables  
2.Running the algorithms  
3.Conclusion   

### Summary:  
The final accuracies we could manage with the four models were -  
Rsquared values for different models  
LASSO model - 0.53440  
RIDGE model - 0.53309  
MLP model - 0.50493  
XGBoost model - 0.55107   
* Capped outliers to median $\pm$ 1.5 * IQR values. This helped improve the accuracy of my models from 0.51 to 0.53.  
* We also removed variables that showed high correlations with other variables in the data using a correlation threshold of 0.6. Although this helped increase accuracy for LASSO model, it had the opposite effect on RIDGE and MLP models.  
* Considering that all categorical variables were changed to numbers before the dataset was provided for predictions, we tried to one-hot encode those variables. This however, led to no improvement in model accuracy and hence it was discarded.  


