## Author attribution  

### Table of contents
* [Introduction](#introduction)
* [Technologies](#technologies)
* [Algorithms](#algorithms)
* [Approach](#approach)

### Introduction
Author attribution is one of the famous NLP technique to identify the author of an unidentified article or to determine the genuine author of a publication when there are multiple claims. In this mini project, I have tried to attribute articles to the respective authors and used multiple classification techniques to come up with the most suitable model for the analysis.

Codes:  
[Analysis md document](https://github.com/akhilesh-reddy/Data-Science-Mini-projects/blob/master/Author%20attribution/Author_attribution.md)  
[Analysis rmd document](https://github.com/akhilesh-reddy/Data-Science-Mini-projects/blob/master/Author%20attribution/Author_attribution.Rmd)

Dataset: Grocery items  

### Technologies  
Rstudio 1.1.453  

### Algorithms  
* K nearest neighbors  
* Random forest  
* XGboost  
* SVM  

### Approach  
1.Read the data in suitable formats required for the exercise  
2.Perform all the pre-processing tasks on the dataset extracted like removing stop words, changing to lower case e.t.c  
3.Create the TF-IDF matrix for both train and test data  
4.Dimensionality reduction  
5.Use the TF-IDF data to create models and compare the accuracy across models to identify the author of a particular article
