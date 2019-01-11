Author Attribution
------------------

This problem calls upon us to use our unsupervised and supervised
learning skills to build models on articles from 50 different authors
and then use these models to predict the authors of some texts provided
in the testing dataset.

### Data preprocessing

Due to the nature of the `tm` library that we are using for creating the
corpus of text files, this problem involves a LOT of data
pre-processing.

### Step 1 - Read in the training text files from individual folders.

The various text files that are to be used for training are stored in
separate folders each with the name of their author. We need to write a
function to extract all these texts and store them in a dataframe.

``` r
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
```

We now have a dataframe with each row a document with its entire text.

### Step 2 - Convert dataframe to corpus

For any text analytics task to be done, we need to convert this to a
corpus and then into a Document-Term matrix. Between these two steps
comes the various data cleaning steps -

-   Convert to lowercase
-   Remove numbers, punctuation marks, excess spaces and stop words from
    the text

``` r
# convert the dataframe to a Corpus
train_corpus_raw <- VCorpus(DataframeSource(text_df_train))

# pre-processing to remove punctuations, spaces, etc.
train_corpus_preproc <- train_corpus_raw
train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(tolower)) # make everything lowercase
train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(removeNumbers)) # remove numbers
train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(removePunctuation)) # remove punctuation
train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(stripWhitespace)) ## remove excess white-space
train_corpus_preproc <- tm_map(train_corpus_preproc, content_transformer(removeWords), stopwords("en")) # remove stop words

# convert the corpus to a document term matrix
DTM_train <- DocumentTermMatrix(train_corpus_preproc)

# remove sparse terms from the DTM_train matrix
DTM_train <- removeSparseTerms(DTM_train, 0.99)
```

This concludes the data preparation step of my analysis. We now need to
use these steps as a template and similarly convert the test text files.
This is done below.

``` r
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

# pre-processing to remove punctuations, spaces, etc.
test_corpus_preproc <- test_corpus_raw
test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(tolower)) # make everything lowercase
test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(removeNumbers)) # remove numbers
test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(removePunctuation)) # remove punctuation
test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(stripWhitespace)) ## remove excess white-space
test_corpus_preproc <- tm_map(test_corpus_preproc, content_transformer(removeWords), stopwords("en")) # remove stop words

# convert the corpus to a document term matrix
DTM_test <- DocumentTermMatrix(test_corpus_preproc, 
                                        control = list(dictionary = Terms(DTM_train)))

# calculate the TF-IDF for each term in the DTM
tfidf_train <- weightTfIdf(DTM_train)
tfidf_test <- weightTfIdf(DTM_test)
```

### Handling missing terms

There are a number of terms in the test DTM document that are not there
in the train matrix. **For the sake of simplicity, we will only keep
terms in the test matrix that are also there in the train matrix!** This
will help us keep a common vocabulary in our Document Term matrices for
author prediction!

### Sparsity Handling

While it is true that words that appear uniquely in some documents might
help significantly in predicting them, words that occur in too few
documents don’t really have any predictive power. For this reason we
look at terms that appear in atleast 1% of all documents. There are
about 782 such unique terms in both the train and the test texts.

### Principal Component Analysis

782 is a lot of features to work with. Let’s use PCA to get the most
important variables from the 782 unique terms that have been shortlisted
above!

``` r
# Lets convert these to a matrix and run PCA
X_train <- as.matrix(tfidf_train)
X_test <- as.matrix(tfidf_test)

pca_train = prcomp(X_train, scale=TRUE)

# we will take the first 350 components because they explain 50% of the variance in the data
plot(summary(pca_train)$importance[3,], main = "PCA Analysis Train", xlab = "Components",
     ylab = "Cumulative % Variance Explained")
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Author_Attribution_files/figure-markdown_github/unnamed-chunk-5-1.png)

A summary of the variance explained shows us that around 350 principal
components explain around 50% of the overall variance! <br>

1.  **We will now use the first 350 PC scores from the PCA analysis as
    our predictors in the train data.**
2.  **We also want our test data to be on the same scale as the train
    data. For this we will scale the test data and multiply by the
    component loadings that we obtained!**

``` r
X_train <- pca_train$x[,1:350]
X_train <- cbind(X_train, class_labels_train)
loading_train <- pca_train$rotation[,1:350]

# multiply to get a test matrix with the principal component values
X_test_pc <- scale(X_test) %*% loading_train
X_test_pc <- as.data.frame(X_test_pc)
```

### Model Training

Now that we have created the training and testing matrices, let’s
convert them to dataframes ready for modelling!

``` r
# convert to a dataframe for use in modelling
X_train <- as.data.frame(X_train)

# convert the numeric variables to numeric class, excluding the class labels.
for (name in names(X_train)){
  if (name == "class_labels_train"){
    next
  }else{
    X_train[[name]] <- as.numeric(as.character(X_train[[name]]))
  }
}

# convert the output class to a factor!
X_train$class_labels_train <- as.factor(X_train$class_labels_train)
```

### Author Attribution Model 1 - K-Nearest Neighbors

It makes sense that documents closer to each other (using similar terms)
in terms of the Manhattan distance would be from the same author. Lets
try K-Nearest Neighbors to predict the author for each document in the
test set! <br>

1.  We will use a K Nearest neighbor model and look for the best K-value
    in the set {5,7,9,11}.
2.  For the distance metric, we will use the Manhattan distance!

``` r
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
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Author_Attribution_files/figure-markdown_github/unnamed-chunk-8-1.png)

The plot shows that using 5 nearest neighbors, we get an overall
accuracy of ~35%. What does the model get right?

    ## # A tibble: 5 x 2
    ##   Actual_Author   Accuracy
    ##   <fct>              <dbl>
    ## 1 TheresePoletti      0.94
    ## 2 LynneO'Donnell      0.84
    ## 3 JoWinterbottom      0.8 
    ## 4 LynnleyBrowning     0.8 
    ## 5 JimGilchrist        0.72

What does the model not get right?

    ## # A tibble: 5 x 2
    ##   Actual_Author  Accuracy
    ##   <fct>             <dbl>
    ## 1 AlexanderSmith     0.02
    ## 2 JaneMacartney      0.02
    ## 3 MichaelConnor      0.02
    ## 4 KarlPenhaul        0.04
    ## 5 EdnaFernandes      0.06

### Author Attribution Model 2 - Random Forest

The accuracy with K Nearest Neighbors isnt good! We don’t want to do
worse than a coin toss! Let’s try out the Random Forest models and check
if we do any better! <br>

1.  We will use a random forest model with 1000 trees with the default
    value of variables to pick for each tree!

``` r
# Random Forest
rf_model <- randomForest(class_labels_train ~ .,
                         data = X_train,
                         ntree = 1000)

author_predict <- predict(rf_model, X_test_pc, type = "response")

answer <- as.data.frame(table(author_predict, class_labels_test))

answer$correct <- ifelse(answer$author_predict==answer$class_labels_test, 1, 0)

rf_accuracy <- sum(answer$Freq[answer$correct==1])*100/sum(answer$Freq)
  
print(paste0("Accuracy is ", rf_accuracy))
```

    ## [1] "Accuracy is 58.88"

The random Forest models give us 58.88% accuracy. This is much better
than the knn model with 35% accuracy.

Let’s see what this model does well -

    ## # A tibble: 5 x 2
    ##   Actual_Author   Accuracy
    ##   <fct>              <dbl>
    ## 1 JimGilchrist        1   
    ## 2 LynnleyBrowning     0.98
    ## 3 KarlPenhaul         0.92
    ## 4 RobinSidel          0.9 
    ## 5 AaronPressman       0.88

What doesn’t the model get right?

    ## # A tibble: 5 x 2
    ##   Actual_Author    Accuracy
    ##   <fct>               <dbl>
    ## 1 TanEeLyn             0.12
    ## 2 DarrenSchuettler     0.16
    ## 3 DavidLawder          0.18
    ## 4 EdnaFernandes        0.18
    ## 5 ScottHillis          0.18

We see from the lowest accuracies that even those are better than the
knn model! This is a good candidate for a prediction model!

### Author Attribution Model 3 - XGBoost model

Finally, let’s run the XGBoost model and check if it is able to improve
upon the accuracy of the random Forest model. We believe so because by
design XGBoost tries to capture the remaining pattern in the residuals
of each previous model.

``` r
# XGBoost model

train_data_xgboost_matrix <- data.matrix(X_train[,1:350])
test_data_xgboost_matrix <- data.matrix(X_test_pc)

dtrain <- xgb.DMatrix(data = train_data_xgboost_matrix, label = as.numeric(X_train[,351]) - 1)
dtest <- xgb.DMatrix(data = test_data_xgboost_matrix, label = as.numeric(as.factor(class_labels_test)) - 1)

boost_model <- xgboost(data = dtrain, # the data   
                       nround = 50, # max number of boosting iterations
                       objective = "multi:softmax",
                       eta = 0.15,
                       num_class = 50,
                       max_depth = 5,
                       eval_metric = "mlogloss",
                       verbose = 0)

author_predict <- predict(boost_model, dtest)
accuracy <- mean(author_predict == (as.numeric(as.factor(class_labels_test)) - 1))*100

print(paste0("Accuracy is ", accuracy))
```

    ## [1] "Accuracy is 52.64"

So, we get 52.64% accuracy with XGboost. This is not better than the
Random Forest model.

### Author Attribution Model 4 - Naive Bayes

Naive Bayes depends on the assumption that each observed term is
independent of the other when it comes to predicting the author! So for
each observed term in the test document term matrix, we calculate the
probability of getting an author if that term is present in the text! We
do this for all available terms and get back the probability of the
document belonging to an author. For all 50 authors, we will choose the
author with the most probability!

``` r
# Naive Bayes Algorithm

# test data frame
test_matrix <- as.matrix(DTM_test)
test_df <- as.data.frame(test_matrix)

rownames(test_df) <- NULL


# training data frame
train_matrix <- as.matrix(DTM_train)
train_df <- as.data.frame(train_matrix)

rownames(train_df) <- NULL

# attach class labels with the dataframe
train_df$train_class <- class_labels_train

# result matrix
result_matrix <- matrix(0, nrow = nrow(test_df), ncol = length(unique(class_labels_train)), 
                        dimnames = list(1:nrow(test_df), unique(class_labels_train)))


for(class in unique(class_labels_train)){
  
  df <- train_df[train_df$train_class == class,]
  
  # add smoothing term for 0 frequency terms
  df[,-ncol(train_df)] <- df[,-ncol(train_df)] + (1/ncol(train_df))
  
  # calculate fraction of appearance of a term
  prob_vector <- (colSums(df[,-ncol(train_df)])/(sum(colSums(df[,-ncol(train_df)]))))
  
  # multiply probability by the test data terms
  probability_vector <- sweep(x = test_df, 
                              MARGIN = 2, 
                              STATS = log(prob_vector), 
                              FUN = '*')
  
  # get one probability number for one author
  probability <- apply(probability_vector, MARGIN = 1, FUN = function(x){sum(x)})
  
  # add these probabilities to the result matrix under one author column
  result_matrix[, class] <- probability
}

# create a dataframe from the result
result_df <- data.frame(result_matrix)

# get predicted author as one with max frequency for each test row
predicted_class <- colnames(result_df)[apply(result_df, 1, which.max)]

# accuracy is where predicted author matches true author
nb_accuracy <- mean(as.numeric(predicted_class == class_labels_test))*100
```

So we see that Naive Bayes achieves an accuracy of 62.32%.

Let’s see which Authors Naive Bayes predicts with the most accuracy!

``` r
# create a frequency table for prediction and actual
answer <- as.data.frame(table(predicted_class, class_labels_test))

# give relevant names to columns
names(answer) <- c("Predicted_Author", "Actual_Author", "Frequency")

# convert from factor to character
answer$Predicted_Author <- as.character(answer$Predicted_Author)
answer$Actual_Author <- as.character(answer$Actual_Author)

# add a dummy for whether the prediction is correct or no
answer$Correct <- ifelse(answer$Predicted_Author == answer$Actual_Author, 1, 0)

# give authors for which we get maximum prediction accuracy
answer %>%
  filter(Predicted_Author == Actual_Author) %>%
  group_by(Actual_Author) %>% 
  summarise("Accuracy" = sum(Frequency)/50) %>%
  arrange(-Accuracy) %>%
  head(5)
```

    ## # A tibble: 5 x 2
    ##   Actual_Author   Accuracy
    ##   <chr>              <dbl>
    ## 1 JimGilchrist        1   
    ## 2 LynnleyBrowning     1   
    ## 3 FumikoFujisaki      0.96
    ## 4 KarlPenhaul         0.94
    ## 5 AaronPressman       0.92

### Conclusion

We see that text mining is not a straightforward task. We read in the
files using the *tm* package, converted them into an easily readable
dataframe which then helped us build our copora for the mining activity.
A large number of english stop words, punctuations, spaces, etc were
removed and Document Term matrices were created. **We managed a max
accuracy of 62.32% using a Naive Bayes model!**

#### Assumptions made

**Terms not appearing in 99% of the documents could be safely removed
before the analysis**. This could potentially be harming the accuracies
that we get with with our models since some very sparse words could have
maybe helped us with identifying some documents better.

**Terms that are in the training documents were used to limit what we
look at from the test documents**. This can also hurt predictions since
some of the sparsely appearing terms in the train data could be more
frequent in the test data. A better approach could be to include all
terms under some pseudo-column and run the analysis again!
