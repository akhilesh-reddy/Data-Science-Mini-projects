### Author attribution

**Objective: To attribute the article in a document to the respective
author using classification models and text analytics**

Let us consider a step by step model to create a classifiation model
from the text data to identify the author of the article

1.Read the data in suitable formats required for the exercise  
2.Perform all the pre-processing tasks on the dataset extracted like
removing stop words, changing to lower case e.t.c  
3.Create the TF-IDF matrix for both train and test data  
4.Dimensionality reduction  
5.Use the TF-IDF data to create models and compare the accuracy across
models to identify the author of a particular article

**1.Read the data in suitable formats**  
As the data is a text file, we will have to convert into a format that
is acceptable for performing the pre-processing functions.After
importing the files, a corpus has been created with all the documents
separately for train and test documents

    library(tm)
    library(proxy)
    library(randomForest)
    library(kknn)
    library(dplyr)
    library(caret)

    # read in train data and create DTM
    author_names_train <- dir("./ReutersC50/C50train")

    file_list_train <- NULL
    class_labels_train <- NULL

    for (name in author_names_train){
      file_list_train <- c(file_list_train, Sys.glob(paste0('./ReutersC50/C50train/', name,'/*.txt')))
      class_labels_train <- c(class_labels_train, rep(name, each = length(Sys.glob(paste0('./ReutersC50/C50train/', name,'/*.txt')))))
    }

    # define the function that will read in the files
    readerPlain = function(fname){
      readPlain(elem = list(content = readLines(fname)), 
                id = fname, language = 'en') }

    # read in the files and store them as a list
    all_files_train <- lapply(file_list_train, readerPlain)

    # give each file a representative name

    file_names_train <- file_list_train %>%
      strsplit("/") %>%
      lapply(tail,n = 2) %>%
      lapply(paste0, collapse = "") %>%
      unlist

    # create a dataframe with doc_id as author-article and text as the text in that article
    text_vector_train <- NULL

    for(i in 1:length(file_names_train)){
      text_vector_train <- c(text_vector_train, paste0(content(all_files_train[[i]]), collapse = " "))
    }

    # dataframe with text and document_id
    text_df_train <- data.frame(doc_id = file_names_train,
                                text = text_vector_train)

    # convert the dataframe to a Corpus
    train_corpus_raw <- VCorpus(DataframeSource(text_df_train))


    # read in the test documents
    author_names_test <- dir("./ReutersC50/C50test")

    file_list_test <- NULL
    class_labels_test <- NULL

    for (name in author_names_test){
      file_list_test <- c(file_list_test, Sys.glob(paste0('./ReutersC50/C50test/', name,'/*.txt')))
      class_labels_test <- c(class_labels_test, rep(name, each = length(Sys.glob(paste0('./ReutersC50/C50test/', name,'/*.txt')))))
    }

    # read in the files and store them as a list
    all_files_test <- lapply(file_list_test, readerPlain)

    # give each file a representative name

    file_names_test <- file_list_test %>%
      strsplit("/") %>%
      lapply(tail,n = 2) %>%
      lapply(paste0, collapse = "") %>%
      unlist

    # create a dataframe with doc_id as author-article and text as the text in that article
    text_vector_test <- NULL

    for(i in 1:length(file_names_test)){
      text_vector_test <- c(text_vector_test, paste0(content(all_files_test[[i]]), collapse = " "))
    }


    # dataframe with text and document_id
    text_df_test <- data.frame(doc_id = file_names_test,
                               text = text_vector_test)

    # convert the dataframe to a Corpus
    test_corpus_raw <- VCorpus(DataframeSource(text_df_test))

**2.Pre-Processing both train and test data**

While dealing with text data, it is optimal to ignore
numbers,punctuations,white spaces as they don't help much in gaining an
insight into the patterns present in the text.It is also important to
ignore the **Stop words**(words like is/an/the e.t.c) as they occur
multiple times with no real information being added to the models.In
this step, we have removed the stop words, punctuations, numbers to
proceed with the analysis

    #train data
    # pre-processing to remove punctuations, spaces, etc.
    train_corpus_preproc <- train_corpus_raw
    train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(tolower))
    train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(removeNumbers)) # remove numbers
    train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(removePunctuation)) # remove punctuation
    train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(stripWhitespace)) ## remove excess white-space
    train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(removeWords), stopwords("en")) # remove stop words


    #test data

    # pre-processing to remove punctuations, spaces, etc.
    test_corpus_preproc <- test_corpus_raw
    test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(tolower)) # make everything lowercase
    test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(removeNumbers)) # remove numbers
    test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(removePunctuation)) # remove punctuation
    test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(stripWhitespace)) ## remove excess white-space
    test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(removeWords), stopwords("en")) # remove stop words

**3.Create TF-IDF matrix for both train and test data**

Next step in this process is to create a TF-IDF matrix of the corpus of
documents that we have created. TF-IDF is a combination of TF(Term
frequency) and IDF(Inverse document frequency). TF gives the number of
times a word occurs in a document while IDF gives less weightage to
words that occur in multiple documents and is not useful in identifying
the style of anyone single author.

After the creation of the TF-IDF matrix, sparsing is one more step that
is recommended while dealing with text data. **In sparsing, we remove
terms that occur less frequently among all the documents**.This is
generally decided by a threshold that is set heuristically based on the
TF-IDF matrix. In this analysis, the threshold is set at **99%(words
that are not present in 99% of the documents will be removed from the
analysis)**

    #train
    # convert the corpus to a document term matrix
    DTM_train <- DocumentTermMatrix(train_corpus_preproc)

    # remove sparse terms from the DTM_train
    DTM_train <- removeSparseTerms(DTM_train, 0.99)


    #test
    # convert the corpus to a document term matrix
    DTM_test <- DocumentTermMatrix(test_corpus_preproc, 
                                   control = list(dictionary = Terms(DTM_train)))

    # calculate the TF-IDF for each term in the DTM
    tfidf_train <- weightTfIdf(DTM_train)
    tfidf_test <- weightTfIdf(DTM_test)

    X_train <- as.matrix(tfidf_train)
    X_test <- as.matrix(tfidf_test)

**4.Dimensionality reduction**

Often while dealing with text data, we encounter with dimensionality
problem. Due to the sheer volume of words present in any language, we
often end up with thousands of words(columns) in the TF-IDF matrix. This
makes it comptutationally heavy for any system to perform any modelling
on the data. Dimensionality is the go to solution in these situations.  
For this problem, i have used **PCA(Prinipal Component Analysis)** to
reduce the number of dimensions in the data set.  
After running PCA, a total of 350 components have been selected for
further analysis as they explain about 50% of the total variaion in the
data.

There is one interseting step that we perform during PCA. As we have 2
different datasets(train and test) to deal with, it is important to
ensure that both the datasets have similar type of principal components
that are aligned towards the same subspace respectively.

**To achieve that, we will use the loadings, rotations and other
attributes from the PC object of the train data to align the components
of the test data in a similar orientation as the train data.**

    pca_train = prcomp(X_train, scale=TRUE)
    # plot(pca_train)

    # we will take the first 350 components because they explain 50% of the variance in the data
    # summary(pca_train)$importance[3,]

    X_train <- pca_train$x[,1:350]
    X_train <- cbind(X_train, class_labels_train)
    loading_train <- pca_train$rotation[,1:350]

    # multiply to get a test matrix with the principal component values
    X_test_pc <- scale(X_test) %*% loading_train
    X_test_pc <- as.data.frame(X_test_pc)

    rm(list = c("all_files_test", "all_files_train", "test_corpus_preproc", "train_corpus_preproc", "text_df_test", "text_df_train",
                "author_names_test", "author_names_train", "file_list_test", "file_list_train", "i", "name", "text_vector_test",
                "text_vector_train", "file_names_test", "file_names_train", "DTM_test", "DTM_train", "pca_train",
                "test_corpus_raw", "train_corpus_raw", "tfidf_train", "tfidf_test"))

    X_train <- as.data.frame(X_train)

    for (name in names(X_train)){
      if (name == "class_labels_train"){
        next
      }else{
        X_train[[name]] <- as.numeric(as.character(X_train[[name]]))
      }
    }

    X_train$class_labels_train <- as.factor(X_train$class_labels_train)
    # 
    # plot(summary(pca_train)$importance[3,], main = "PCA Analysis Train", xlab = "Components",
    #      ylab = "Cumulative % Variance Explained")

**5. Models using features from PCA and identifying the authors**

**Attribution Model 1: knn**

It makes sense that documents closer to each other (using similar terms)
in terms of the Manhattan distance would be from the same author. Lets
try K-Nearest Neighbors to predict the author for each document in the
test set!

1.We will use a K Nearest neighbor model and look for the best K-value
in the set {5,7,9,11}.  
2.For the distance metric, we will use the Manhattan distance!

    # knn model - 29% max accuracy at k = 9
    library(kknn)

    # a vector to store the accuracies of the knn model
    accuracies <- NULL

    # try knn with 5,7,9 and 11 nearest neighbors
    for (i in c(5,7,9,11)){
      knn_model <- kknn(class_labels_train ~ .,
                        X_train,
                        X_test_pc,
                        distance = 1,
                        k= i,
                        kernel = 'rectangular')
      
      accuracies <- c(accuracies,sum(knn_model$fitted.values == class_labels_test)/length(class_labels_test))
    }

    plot(c(5,7,9,11), accuracies, main = "KNN accuracy vs K", xlab = "K-Values", ylab = "Accuracy Score", lty = 1)

![](author_attribution-_files/figure-markdown_strict/unnamed-chunk-5-1.png)
With Knn, we get an overall accuracy of ~35%. Let's see how the model
worked for different authors

Following are the top authors that the model gets right!

    ## # A tibble: 5 x 2
    ##   Actual_Author   Accuracy
    ##   <fct>              <dbl>
    ## 1 TheresePoletti      0.94
    ## 2 LynneO'Donnell      0.84
    ## 3 JoWinterbottom      0.8 
    ## 4 LynnleyBrowning     0.8 
    ## 5 JimGilchrist        0.72

Following are the authors that the model performs very badly

    ## # A tibble: 5 x 2
    ##   Actual_Author  Accuracy
    ##   <fct>             <dbl>
    ## 1 AlexanderSmith     0.02
    ## 2 JaneMacartney      0.02
    ## 3 MichaelConnor      0.02
    ## 4 KarlPenhaul        0.04
    ## 5 EdnaFernandes      0.06

**Attribution model 2: Random Forest**  
Let's run a random forest to check the accuracy of the predictions for
author attribution

    # Random Forest
    rf_model <- randomForest(class_labels_train ~ .,
                             data = X_train,
                             ntree = 1000)
    author_predict <- predict(rf_model, X_test_pc, type = "response")
    answer <- as.data.frame(table(author_predict, class_labels_test))
    answer$correct <- ifelse(answer$author_predict==answer$class_labels_test, 1, 0)

    answer_rf = answer %>% group_by(correct) %>% summarise("Correct" = sum(Freq))

    rf_accuracy <- sum(answer$Freq[answer$correct==1])*100/sum(answer$Freq)
      
    print(paste0("Accuracy is ", rf_accuracy))

    ## [1] "Accuracy is 58.6"

-   Accuracy of Random Forest is 58.6 which is better than knn

Lets see what authors have been attributed with higher accuracy

    ## # A tibble: 5 x 2
    ##   Actual_Author   Accuracy
    ##   <fct>              <dbl>
    ## 1 JimGilchrist        1   
    ## 2 LynnleyBrowning     1   
    ## 3 KarlPenhaul         0.92
    ## 4 AaronPressman       0.86
    ## 5 RobinSidel          0.86

-   Authors that the model predicted with minimum accuracy

<!-- -->

    ## # A tibble: 5 x 2
    ##   Actual_Author    Accuracy
    ##   <fct>               <dbl>
    ## 1 ScottHillis          0.14
    ## 2 EdnaFernandes        0.16
    ## 3 TanEeLyn             0.16
    ## 4 BenjaminKangLim      0.18
    ## 5 DarrenSchuettler     0.18

**Attribution model 3:** **XGBoost**

    # XGBoost model
    library(xgboost)
    train_data_xgboost_matrix <- data.matrix(X_train[,1:350])
    test_data_xgboost_matrix <- data.matrix(X_test_pc)
    class_labels_train = data.matrix(class_labels_train)

    dtrain <- xgb.DMatrix(data = train_data_xgboost_matrix, label = as.numeric(X_train[,351])-1,
                          missing = NA)
    dtest <- xgb.DMatrix(data = test_data_xgboost_matrix, label = as.numeric(as.factor(class_labels_test)) - 1)

    boost_model <- xgboost(data = dtrain,
                           nround = 30, # max number of boosting iterations
                           # distribution = "multinomial",
                           objective = "multi:softmax",
                           eta = 0.1,
                           num_class = 50,
                           max_depth = 3
                           )

    ## [1]  train-merror:0.370400 
    ## [2]  train-merror:0.271200 
    ## [3]  train-merror:0.214800 
    ## [4]  train-merror:0.192800 
    ## [5]  train-merror:0.167200 
    ## [6]  train-merror:0.152400 
    ## [7]  train-merror:0.133600 
    ## [8]  train-merror:0.122800 
    ## [9]  train-merror:0.109200 
    ## [10] train-merror:0.098000 
    ## [11] train-merror:0.089200 
    ## [12] train-merror:0.085600 
    ## [13] train-merror:0.077600 
    ## [14] train-merror:0.072800 
    ## [15] train-merror:0.066000 
    ## [16] train-merror:0.058400 
    ## [17] train-merror:0.050800 
    ## [18] train-merror:0.047200 
    ## [19] train-merror:0.043600 
    ## [20] train-merror:0.039200 
    ## [21] train-merror:0.036400 
    ## [22] train-merror:0.030800 
    ## [23] train-merror:0.028800 
    ## [24] train-merror:0.024800 
    ## [25] train-merror:0.020400 
    ## [26] train-merror:0.019200 
    ## [27] train-merror:0.017200 
    ## [28] train-merror:0.014800 
    ## [29] train-merror:0.012400 
    ## [30] train-merror:0.011200

    author_predict <- predict(boost_model, dtest)
    XGbacc <- mean(author_predict == (as.numeric(as.factor(class_labels_test)) - 1))*100
    print(paste0("Accuracy is :",XGbacc))

    ## [1] "Accuracy is :47.72"

We get 47.72 accuracy from XGBoost which is not better than the Random
forest model

**Attribution model 4: SVM **

    library(e1071)
    X_train_svm = subset(X_train, select = -class_labels_train)
    y_train_svm = as.factor(class_labels_train)

    model_svm = svm(X_train_svm, y_train_svm, probability = TRUE)
    pred_prob = predict(model_svm, X_test_pc, decision.values = TRUE, probability = TRUE)

    cm_svm = table(pred_prob,class_labels_test)
    accuracy_svm = sum(diag(cm_svm)) / sum(cm_svm)
    print(paste0("Accuracy is :",accuracy_svm*100))

    ## [1] "Accuracy is :56.32"

**Conclusion:**

Text analytics is often computationally heavy especially when dealing
with thousands of documents. Dimensionality reduction is the best way to
go further with the analysis.There is also a downside to this as
removing the terms from the documents might result in reduced accuracies
as we have seen earlier. But that is trade-off that is essential while
dealing with data of this scale.  
Random forest gave us the best accuracy among all the other models with
a value of about ~60%. This can be further improved if we can do an
ensemble of the above models that are being used.
