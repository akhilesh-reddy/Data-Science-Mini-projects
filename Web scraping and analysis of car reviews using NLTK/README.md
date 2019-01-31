## CAR BRAND ASSOCIATION ANALYSIS USING WEB SCRAPED DATA

### Table of contents
* [Introduction](#introduction)
* [Technologies](#technologies)
* [Algorithms](#algorithms)
* [Approach](#approach)
* [Insights](#insights)

### Introduction
Understanding how customers are associating a brand with their own sentiments is crucial information to growth in industries. In this mini project, we have found associations between luxury cars discussed in Edmund’s forum and generated insights regarding what attributes are customers talking about when it comes to these brands  

Codes:  
[Car brand association analysis using web scraped data.ipynb](https://github.com/akhilesh-reddy/Data-Science-Mini-projects/blob/master/Web%20scraping%20and%20analysis%20of%20car%20reviews%20using%20NLTK/Car%20brand%20association%20analysis%20using%20web%20scraped%20data.ipynb)  has the code for the analysis  

Dataset : Data scraped from Edmunds forum using Selenium  

### Technologies  
Project is created with:  
* Python 3.6.5  
* Selenium  
* Similarity measures
 
### Algorithms
* Lift analysis using similarity measures  
* Multidimensional scaling plot  

### Approach:  

1. Obtaining the data through web scraping  
2. Data pre-processing  
3. Visualizing brand association on an Multi dimensional scaling plot  
4. Primary attributes associated with the brand  
5. Finding the aspirational brand that customers want to buy  

### Insights:  
* BMW is far from being the "ultimate driving machine" on Edmunds. BMW and the different car attributes have comparitively low lift values to the other top brands. This implies that when people are talking about different desirable car features, BMW does not come to mind  
* Honda's advertising manager is working with a product with several marketable strengths, such as efficiency, safety, and comfort. If the manager could do more to get people to associate performance with Honda, then Honda would have the strongest reputation that it possibly could among Edmunds forum users     
* Mercedes needs to market more of its features other than comfort, as it is the only feature Mercedes is the prime example for (in the top 5 brands)
* Audi and Acura both have styling as a part of their brand reputations.Advertising managers for these companies need not worry about the other since our MDS plot shows that Audi and Acura are dissimilar enough to imply that their markets have only small intersections * * Lastly, BMW needs to do a better job at marketing its product overall  
