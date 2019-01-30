## Author attribution  

### Table of contents
* [Introduction](#introduction)
* [Technologies](#technologies)
* [Algorithms](#algorithms)
* [Approach](#approach)

### Introduction
Author attribution is one of the famous NLP technique to identify the author of an unidentified article or to determine the genuine author of a publication when there are multiple claims. In this mini project, I have tried to attribute articles to the respective authors and used multiple classification techniques to come up with the most suitable model for the analysis.

Codes:  
[Analysis md document](https://github.com/akhilesh-reddy/Data-Science-Mini-projects/blob/master/Association%20rules/Association%20rules.md)  
[Analysis rmd document](https://github.com/akhilesh-reddy/Data-Science-Mini-projects/blob/master/Association%20rules/Association%20rules.rmd)

Dataset: Grocery items  

### Technologies  
Project is created with:  
* R studio  
* Gephi network visualization tool
 
### Algorithms
* Apriori algorithm

### Approach:  

Steps followed to create the association rules  

1.Data pre processing  
2.Running the apriori algorithm  
3.Visualization of the Output  

### Insights:  
Following are the rules that came out to be siginificant based on the support and lift that we considered:  

1.There is a high probability for people to red/blush wine if they buy bottled beer and liquor  
2.Buying soda and popcorn has a high association with salty snack  
3.Processed cheese and white bread has a high association with buying ham based on the lift  
4.Some rules are obvious like buying flour and baking powder has a high association with sugar  
5.Dairy products are associated with each other  
6.Ham, processed cheese and butter are highly associated  
