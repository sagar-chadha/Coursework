H1B Visa Analysis
-----------------

In this particular project, my team and I look at 500,000 rows of visa
applications from 2011 to 2017 and try to predict with the maximum
possible accuracy, the acceptance or denial of a visa application.

Let’s look at the data and see what variables we have.

``` r
cleaned_data <- readRDS("FinalDataset_Group 3.RDS")
glimpse(cleaned_data)
```

    ## Observations: 501,631
    ## Variables: 14
    ## $ sub_mon          <int> 2, 3, 3, 2, 3, 2, 2, 2, 2, 2, 3, 3, 3, 3, 3, ...
    ## $ de_mon           <int> 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 1...
    ## $ workers          <dbl> -0.2677385, -0.2677385, 0.5638029, -0.2677385...
    ## $ fulltime         <fct> Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, ...
    ## $ pay_unit         <fct> Year, Year, Year, Year, Year, Year, Year, Yea...
    ## $ h1bdepen         <fct> N, N, Y, N, N, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, ...
    ## $ willful_violator <fct> N, N, N, N, N, N, N, N, N, N, N, N, N, N, N, ...
    ## $ status           <fct> CERTIFIED, CERTIFIED, CERTIFIED, CERTIFIED, C...
    ## $ STEM             <fct> Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, Y, N, N, N, ...
    ## $ employer_region  <fct> MIDWEST, MIDWEST, SOUTH, SOUTH, SOUTH, NORTHE...
    ## $ worksite_region  <fct> MIDWEST, MIDWEST, SOUTH, NORTHEAST, SOUTH, NO...
    ## $ Sub_to_dec       <dbl> 3.507896, 3.463333, 3.432560, 4.558584, 4.525...
    ## $ wage             <dbl> -0.47368343, -0.98374171, -0.10374994, 1.1750...
    ## $ soc_new          <fct> Group5, Group5, Group5, Group5, Group5, Group...

The predictors are as follows - <br> \* `sub_mon` is the month of
submission of the visa application. \* `de_mon` is the month the
decision was provided. \* `workers` is the standardized number of
workers with H1B already working for the employer. \* `fulltime` denotes
whether the job is full time or not. \* `pay_unit` is the job one that
pays yearly, monthly, hourly or weekly. \* `h1bdepen` signifies whether
the candidate has dependents. \* `willful_violator` whether the employer
has, in the past, violated H1B rules \* `status` is the target variable
- ‘Certified’ or ‘Denied’ \* `STEM` whether the employment comes under
the STEM category \* `employer_region`, `worksite_region` region of the
employer headquarters and region of employment \* `sub_to_dec` scaled
time interval between submission of application to decision \* `wage`
standardised pay scale of the employment \* `soc_new` denotes a group
name for the type of employment

Exploratory Data Analysis
-------------------------

Let’s look through the data and see if we find any patterns that can
help us predict the outcome.

### Status v/s Waiting time between submission and decision

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-3-1.png)

There is a clear pattern here - applications that wait longer are
generally certified. Decisions that are quickly made are generally
‘Denials’

### Status v/s Wages

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-4-1.png)

We don’t find much of a pattern here. Both categories have wages that
look pretty similar in distribution.

### Status v/s application submission month

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-5-1.png)

We find denial rates changing slightly with application submission
months but not much.

### decision month vs denial rate

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-6-1.png)

Model training
--------------

Let’s now try to fit a few models and predict the application status.

### Training and testing data

We will split the data into training and testing portions with a 90-10
split.

``` r
ds <- cleaned_data # Copy the data to ds variable

ds$sub_mon <- as.factor(ds$sub_mon)
ds$de_mon <- as.factor(ds$de_mon)

train <- sample(dim(ds)[1],dim(ds)[1]*.9) # 90/10 training testing split
```

### Logistic Regression

We define ‘Denied’ as positive class since that is the rare class that
we are interested to predict.

``` r
ds.train <- ds[train,] # Set up training set
ds.test <- ds[-train,] # Set up testing set
logit <- glm(status~., data=ds.train, family = "binomial") # Fit the model
```

    ## Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

``` r
summary(logit)
```

    ## 
    ## Call:
    ## glm(formula = status ~ ., family = "binomial", data = ds.train)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.9560  -0.1107  -0.0730  -0.0479   4.1724  
    ## 
    ## Coefficients:
    ##                          Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)              -0.02777    0.58793  -0.047  0.96233    
    ## sub_mon2                  0.80738    0.15108   5.344 9.09e-08 ***
    ## sub_mon3                  1.45148    0.18363   7.905 2.69e-15 ***
    ## sub_mon4                 -0.01345    0.21285  -0.063  0.94963    
    ## sub_mon5                 -0.86169    0.26165  -3.293  0.00099 ***
    ## sub_mon6                 -0.26704    0.39775  -0.671  0.50198    
    ## sub_mon7                  4.83987   95.00947   0.051  0.95937    
    ## sub_mon8                  1.36412   88.07395   0.015  0.98764    
    ## sub_mon9                  0.24233    0.40314   0.601  0.54776    
    ## sub_mon10                -0.36191    0.35911  -1.008  0.31356    
    ## sub_mon11                 0.63635    0.28970   2.197  0.02805 *  
    ## sub_mon12                 1.25923    0.19767   6.370 1.89e-10 ***
    ## de_mon2                  -0.97676    0.15592  -6.265 3.74e-10 ***
    ## de_mon3                  -2.07597    0.19188 -10.819  < 2e-16 ***
    ## de_mon4                  -0.45237    0.21066  -2.147  0.03176 *  
    ## de_mon5                   0.42201    0.25962   1.625  0.10406    
    ## de_mon6                  -0.23790    0.39575  -0.601  0.54775    
    ## de_mon10                  0.08383    0.36186   0.232  0.81680    
    ## de_mon11                 -0.56692    0.29372  -1.930  0.05359 .  
    ## de_mon12                 -1.70961    0.20297  -8.423  < 2e-16 ***
    ## workers                   0.03318    0.01564   2.121  0.03391 *  
    ## fulltimeY                 0.44772    0.08841   5.064 4.10e-07 ***
    ## pay_unitHour             -1.60936    0.56609  -2.843  0.00447 ** 
    ## pay_unitMonth            -0.90547    0.63877  -1.418  0.15633    
    ## pay_unitWeek             -0.32402    0.69733  -0.465  0.64217    
    ## pay_unitYear             -2.77393    0.56381  -4.920 8.66e-07 ***
    ## h1bdepenY                -1.00713    0.04073 -24.724  < 2e-16 ***
    ## willful_violatorY         1.74710    0.37996   4.598 4.26e-06 ***
    ## STEMY                     0.22929    0.04167   5.503 3.73e-08 ***
    ## employer_regionNORTHEAST -0.03786    0.06239  -0.607  0.54396    
    ## employer_regionSOUTH      0.07064    0.06127   1.153  0.24897    
    ## employer_regionWEST       0.20442    0.07087   2.884  0.00392 ** 
    ## worksite_regionNORTHEAST  0.40144    0.06213   6.462 1.04e-10 ***
    ## worksite_regionSOUTH      0.41321    0.06045   6.836 8.16e-12 ***
    ## worksite_regionWEST       0.22138    0.06828   3.242  0.00119 ** 
    ## Sub_to_dec               -5.16396    0.07092 -72.812  < 2e-16 ***
    ## wage                     -0.03133    0.01087  -2.882  0.00396 ** 
    ## soc_newGroup2            -1.20064    0.11702 -10.261  < 2e-16 ***
    ## soc_newGroup3            -1.84662    0.10090 -18.301  < 2e-16 ***
    ## soc_newGroup4            -3.28105    0.10675 -30.737  < 2e-16 ***
    ## soc_newGroup5            -4.56588    0.10492 -43.519  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 60006  on 451466  degrees of freedom
    ## Residual deviance: 36725  on 451426  degrees of freedom
    ## AIC: 36807
    ## 
    ## Number of Fisher Scoring iterations: 17

A summary of the model shows - \* Positive coefficients on months 2 and
3 which means odds of denial increase in these months. \* Suprisingly
full time jobs also have slightly more odds of denial \* pay unit ‘year’
has a large negative coefficient i.e. reduces odds of denial

Let’s look at a confusion matrix to see how the model performs on the
testing data.

    ## Confusion matrix for logistic Regression -

    ##            preds.log
    ##             CERTIFIED DENIED
    ##   CERTIFIED     49518     11
    ##   DENIED          423    212

    ## 
    ##  False Negative Rate:

    ## 66.61 %

Our logistic model captures most of the certified cases, which is
expected. As far as denied cases go, we are capturing about 33.5% of the
actual denied cases which is not very good! A lot of the actually denied
cases are being predicted as Certified by the model, we dont want that.

### Stepwise Logistic Regression

Previously we tried using all the variables we had to build our model.
This strategy can backfire if there’s many variables that add no value
to the prediction. Lets use the stepwise predictor selection strategy
and see if we can do better!

We will use the backward strategy, i.e. we will fit all variables and
then try to cut down on variables that aren’t important.

``` r
logit.step <- step(logit,direction = "backward")
```

    ## Start:  AIC=36807.18
    ## status ~ sub_mon + de_mon + workers + fulltime + pay_unit + h1bdepen + 
    ##     willful_violator + STEM + employer_region + worksite_region + 
    ##     Sub_to_dec + wage + soc_new
    ## 
    ##                    Df Deviance   AIC
    ## <none>                   36725 36807
    ## - workers           1    36730 36810
    ## - wage              1    36734 36814
    ## - employer_region   3    36742 36818
    ## - willful_violator  1    36740 36820
    ## - fulltime          1    36752 36832
    ## - STEM              1    36756 36836
    ## - worksite_region   3    36785 36861
    ## - sub_mon          11    37064 37124
    ## - pay_unit          4    37175 37249
    ## - de_mon            8    37251 37317
    ## - h1bdepen          1    37430 37510
    ## - soc_new           4    41725 41799
    ## - Sub_to_dec        1    44693 44773

``` r
# formula(logit.step) gives back the full model
# Model from backwards selection below
# logit.step <- glm(status ~ sub_mon + de_mon + workers + fulltime + pay_unit + h1bdepen + willful_violator +
#                    STEM + employer_region + worksite_region + Sub_to_dec + wage + 
#                    soc_new,data=ds.train,family = 'binomial')
summary(logit.step)
```

    ## 
    ## Call:
    ## glm(formula = status ~ sub_mon + de_mon + workers + fulltime + 
    ##     pay_unit + h1bdepen + willful_violator + STEM + employer_region + 
    ##     worksite_region + Sub_to_dec + wage + soc_new, family = "binomial", 
    ##     data = ds.train)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.9560  -0.1107  -0.0730  -0.0479   4.1724  
    ## 
    ## Coefficients:
    ##                          Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)              -0.02777    0.58793  -0.047  0.96233    
    ## sub_mon2                  0.80738    0.15108   5.344 9.09e-08 ***
    ## sub_mon3                  1.45148    0.18363   7.905 2.69e-15 ***
    ## sub_mon4                 -0.01345    0.21285  -0.063  0.94963    
    ## sub_mon5                 -0.86169    0.26165  -3.293  0.00099 ***
    ## sub_mon6                 -0.26704    0.39775  -0.671  0.50198    
    ## sub_mon7                  4.83987   95.00947   0.051  0.95937    
    ## sub_mon8                  1.36412   88.07395   0.015  0.98764    
    ## sub_mon9                  0.24233    0.40314   0.601  0.54776    
    ## sub_mon10                -0.36191    0.35911  -1.008  0.31356    
    ## sub_mon11                 0.63635    0.28970   2.197  0.02805 *  
    ## sub_mon12                 1.25923    0.19767   6.370 1.89e-10 ***
    ## de_mon2                  -0.97676    0.15592  -6.265 3.74e-10 ***
    ## de_mon3                  -2.07597    0.19188 -10.819  < 2e-16 ***
    ## de_mon4                  -0.45237    0.21066  -2.147  0.03176 *  
    ## de_mon5                   0.42201    0.25962   1.625  0.10406    
    ## de_mon6                  -0.23790    0.39575  -0.601  0.54775    
    ## de_mon10                  0.08383    0.36186   0.232  0.81680    
    ## de_mon11                 -0.56692    0.29372  -1.930  0.05359 .  
    ## de_mon12                 -1.70961    0.20297  -8.423  < 2e-16 ***
    ## workers                   0.03318    0.01564   2.121  0.03391 *  
    ## fulltimeY                 0.44772    0.08841   5.064 4.10e-07 ***
    ## pay_unitHour             -1.60936    0.56609  -2.843  0.00447 ** 
    ## pay_unitMonth            -0.90547    0.63877  -1.418  0.15633    
    ## pay_unitWeek             -0.32402    0.69733  -0.465  0.64217    
    ## pay_unitYear             -2.77393    0.56381  -4.920 8.66e-07 ***
    ## h1bdepenY                -1.00713    0.04073 -24.724  < 2e-16 ***
    ## willful_violatorY         1.74710    0.37996   4.598 4.26e-06 ***
    ## STEMY                     0.22929    0.04167   5.503 3.73e-08 ***
    ## employer_regionNORTHEAST -0.03786    0.06239  -0.607  0.54396    
    ## employer_regionSOUTH      0.07064    0.06127   1.153  0.24897    
    ## employer_regionWEST       0.20442    0.07087   2.884  0.00392 ** 
    ## worksite_regionNORTHEAST  0.40144    0.06213   6.462 1.04e-10 ***
    ## worksite_regionSOUTH      0.41321    0.06045   6.836 8.16e-12 ***
    ## worksite_regionWEST       0.22138    0.06828   3.242  0.00119 ** 
    ## Sub_to_dec               -5.16396    0.07092 -72.812  < 2e-16 ***
    ## wage                     -0.03133    0.01087  -2.882  0.00396 ** 
    ## soc_newGroup2            -1.20064    0.11702 -10.261  < 2e-16 ***
    ## soc_newGroup3            -1.84662    0.10090 -18.301  < 2e-16 ***
    ## soc_newGroup4            -3.28105    0.10675 -30.737  < 2e-16 ***
    ## soc_newGroup5            -4.56588    0.10492 -43.519  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 60006  on 451466  degrees of freedom
    ## Residual deviance: 36725  on 451426  degrees of freedom
    ## AIC: 36807
    ## 
    ## Number of Fisher Scoring iterations: 17

So the backward selection model shows that removing any variable would
lead to an increase in the AIC scores. All variables that we have
captured are actually important.

``` r
# Prediction error
preds.step <- predict(logit.step,ds.test[,-8],type = 'response') # Generate predictions between 0 and 1
preds.log.step <- rep("CERTIFIED",length(preds.step))
preds.log.step[preds.step >= .5] <- "DENIED" 
t.step <- table(ds.test$status,preds.log.step)

 # Confusion matrix
cat("Confusion matrix for stepwise logistic Regression - \n")
```

    ## Confusion matrix for stepwise logistic Regression -

``` r
t.step # Confusion matrix
```

    ##            preds.log.step
    ##             CERTIFIED DENIED
    ##   CERTIFIED     49518     11
    ##   DENIED          423    212

``` r
cat("\n False Negative Rate: \n")
```

    ## 
    ##  False Negative Rate:

``` r
round(t.step[2,1]*100/sum(t.step[2,]),2) # 65.169% FN error
```

    ## [1] 66.61

We see a large false negative rate for these problems, i.e. for cases
where the application was actually denied, we are predicting that it
will be certified. This isn’t a good result for our model! We need to
make sure that False Negatives are minimized while still maintaining a
fair level of accuracy.

### Threshold selection

Lets look at various threshold values and their corresponding error
rates and False negative error rates. We will use this chart to select
an optimum value for threshold based on our requirements.

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-12-1.png)

From the above plot, we can choose threshold = 0.08. Lets look at the
various error rates with the chosen value of threshold.

    ## Confusion matrix with the new threshold -

    ##            preds.log.thre
    ##             CERTIFIED DENIED
    ##   CERTIFIED     48886    643
    ##   DENIED          261    374

    ## 
    ## False negative rate with the new threshold -

    ## 41.1 %

### Linear Discriminant Analysis

``` r
## LDA

library (MASS) 
```

    ## 
    ## Attaching package: 'MASS'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     select

``` r
# Fit LDA model
m_lda=lda(status~.,data=ds.train) 

lda.pred=predict(m_lda,ds.test[,-8])

lda_pred=lda.pred$class

cat("Confusion Matrix for the LDA model - \n")
```

    ## Confusion Matrix for the LDA model -

``` r
t.lda <- table(ds.test$status, lda_pred)
t.lda
```

    ##            lda_pred
    ##             CERTIFIED DENIED
    ##   CERTIFIED     47945   1584
    ##   DENIED          300    335

``` r
cat("\n False negative rate - \n")
```

    ## 
    ##  False negative rate -

``` r
cat(round(t.lda[2,1]*100/sum(t.lda[2,]),2),"%")
```

    ## 47.24 %

``` r
# Set equal prior probabilities

m_lda2=lda(status~sub_mon+de_mon+fulltime+pay_unit+h1bdepen
          +willful_violator+STEM+employer_region+worksite_region
          +Sub_to_dec+wage,data=ds.train,prior=c(0.5,0.5))

lda.pred2=predict(m_lda2,ds.test[,-8])
lda_pred2=lda.pred2$class

cat("Confusion Matrix for the updated LDA model - \n")
```

    ## Confusion Matrix for the updated LDA model -

``` r
table(ds.test$status, lda_pred2)
```

    ##            lda_pred2
    ##             CERTIFIED DENIED
    ##   CERTIFIED     41019   8510
    ##   DENIED          277    358

### Random Forest Model

Finally let’s try a random forest and XGBoost models to see how they
perform on this data. Random forest creates a large number of
uncorrelated tree models and averages the prediction from them to get
the final prediction. Since our predictors are important, we should hope
that different trees would capture different patterns and the overall
accuracy of the model would get better.

``` r
library(randomForest)

# Iterate through different numbers of trees to find the best one
for (i in c(50,100)){
  status.rf.trees = randomForest(formula = status ~ ., data = ds.train, ntree = i, 
                                 proximity = F, mtry = 4)
  pred.bag.trees = predict(status.rf.trees, newdata = ds.test, type = "response" )
  print(table(pred.bag.trees,ds.test$status)[2,2]/(table(pred.bag.trees,ds.test$status)[1,2]+table(pred.bag.trees,ds.test$status)[2,2]))
}
```

    ## [1] 0.5700787
    ## [1] 0.5685039

We see from the above iterations (printed values are accuracies) that 50
is the best number of trees. It costs too much time to run 250 trees for
only 0.2% improvement. Next, lets check for the best value for the
threshold.

``` r
# Iterate through different sets of thresholds to find the best set
for (i in seq(0.05, 0.5, 0.05)){
  status.rf.c = randomForest(formula = status ~ ., data = ds.train, ntree = 50,
                           proximity = F, mtry = 4, cutoff=c(i,1-i))
  pred.bag.c = predict(status.rf.c, newdata = ds.test, type = "response" )
  print(table(pred.bag.c,ds.test$status)[2,2]/(table(pred.bag.c,ds.test$status)[1,2]+ table(pred.bag.c,ds.test$status)[2,2]))
  # This also prints accuracy, not error
}
```

    ## [1] 0.4330709
    ## [1] 0.4582677
    ## [1] 0.4897638
    ## [1] 0.4992126
    ## [1] 0.503937
    ## [1] 0.5181102
    ## [1] 0.5338583
    ## [1] 0.5464567
    ## [1] 0.5590551
    ## [1] 0.5716535

``` r
# The best threshold was the default threshold.
```

The best threshold is the default threshold of 0.5. Let’s run the best
model on our data and see how it does on the test data.

``` r
# Random forest with tuned ntree and prediction threshold.
status.rf = randomForest(formula = status ~ ., data=ds.train, ntree=50, proximity=F, mtry = 4)
varImpPlot(status.rf, main="Importance for Random Forest")
```

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-17-1.png)

``` r
# Bagging model with the 3 best variables as identified by Importance plot
status.bag = randomForest(formula = status ~ wage + Sub_to_dec + soc_new, data=ds.train, ntree=50, proximity=F, mtry = 3)
varImpPlot(status.bag)
```

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-17-2.png)

``` r
pred.bag = predict(status.bag,newdata = ds.test, type = "response")

t_rf <- table(ds.test$status,pred.bag) # 46.102% Error

cat("Confusion matrix for this model - \n")
```

    ## Confusion matrix for this model -

``` r
t_rf
```

    ##            pred.bag
    ##             CERTIFIED DENIED
    ##   CERTIFIED     49432     97
    ##   DENIED          290    345

``` r
cat("False negative rate - \n")
```

    ## False negative rate -

``` r
cat(round(t_rf[2,1]*100/sum(t_rf[2,]),2),"%")
```

    ## 45.67 %

### XG Boost Model

XGBoost only works with data matrix format. Let’s convert our data to
that format and try building a model using XGBoost.

``` r
# XGBoost algorithm for classification
bst <- xgboost(data = sparse_matrix, label = output_vector, max_depth = 10,
               eta = 0.3, nthread = 2, nrounds = 200, objective = "binary:logistic")
```

    ## [1]  train-error:0.006045 
    ## [2]  train-error:0.005996 
    ## [3]  train-error:0.005994 
    ## [4]  train-error:0.005945 
    ## [5]  train-error:0.005938 
    ## [6]  train-error:0.005930 
    ## [7]  train-error:0.005903 
    ## [8]  train-error:0.005861 
    ## [9]  train-error:0.005823 
    ## [10] train-error:0.005819 
    ## [11] train-error:0.005735 
    ## [12] train-error:0.005710 
    ## [13] train-error:0.005704 
    ## [14] train-error:0.005653 
    ## [15] train-error:0.005622 
    ## [16] train-error:0.005624 
    ## [17] train-error:0.005600 
    ## [18] train-error:0.005584 
    ## [19] train-error:0.005520 
    ## [20] train-error:0.005473 
    ## [21] train-error:0.005453 
    ## [22] train-error:0.005431 
    ## [23] train-error:0.005394 
    ## [24] train-error:0.005389 
    ## [25] train-error:0.005367 
    ## [26] train-error:0.005349 
    ## [27] train-error:0.005340 
    ## [28] train-error:0.005320 
    ## [29] train-error:0.005318 
    ## [30] train-error:0.005307 
    ## [31] train-error:0.005292 
    ## [32] train-error:0.005250 
    ## [33] train-error:0.005247 
    ## [34] train-error:0.005232 
    ## [35] train-error:0.005212 
    ## [36] train-error:0.005185 
    ## [37] train-error:0.005174 
    ## [38] train-error:0.005174 
    ## [39] train-error:0.005159 
    ## [40] train-error:0.005152 
    ## [41] train-error:0.005152 
    ## [42] train-error:0.005137 
    ## [43] train-error:0.005126 
    ## [44] train-error:0.005103 
    ## [45] train-error:0.005090 
    ## [46] train-error:0.005070 
    ## [47] train-error:0.005055 
    ## [48] train-error:0.005052 
    ## [49] train-error:0.005044 
    ## [50] train-error:0.005039 
    ## [51] train-error:0.005026 
    ## [52] train-error:0.005024 
    ## [53] train-error:0.005017 
    ## [54] train-error:0.005010 
    ## [55] train-error:0.005004 
    ## [56] train-error:0.004999 
    ## [57] train-error:0.005001 
    ## [58] train-error:0.004966 
    ## [59] train-error:0.004962 
    ## [60] train-error:0.004944 
    ## [61] train-error:0.004928 
    ## [62] train-error:0.004900 
    ## [63] train-error:0.004891 
    ## [64] train-error:0.004893 
    ## [65] train-error:0.004891 
    ## [66] train-error:0.004880 
    ## [67] train-error:0.004855 
    ## [68] train-error:0.004851 
    ## [69] train-error:0.004853 
    ## [70] train-error:0.004846 
    ## [71] train-error:0.004840 
    ## [72] train-error:0.004831 
    ## [73] train-error:0.004831 
    ## [74] train-error:0.004818 
    ## [75] train-error:0.004811 
    ## [76] train-error:0.004802 
    ## [77] train-error:0.004800 
    ## [78] train-error:0.004793 
    ## [79] train-error:0.004784 
    ## [80] train-error:0.004769 
    ## [81] train-error:0.004756 
    ## [82] train-error:0.004753 
    ## [83] train-error:0.004738 
    ## [84] train-error:0.004736 
    ## [85] train-error:0.004731 
    ## [86] train-error:0.004731 
    ## [87] train-error:0.004725 
    ## [88] train-error:0.004725 
    ## [89] train-error:0.004707 
    ## [90] train-error:0.004702 
    ## [91] train-error:0.004698 
    ## [92] train-error:0.004678 
    ## [93] train-error:0.004674 
    ## [94] train-error:0.004665 
    ## [95] train-error:0.004654 
    ## [96] train-error:0.004654 
    ## [97] train-error:0.004649 
    ## [98] train-error:0.004645 
    ## [99] train-error:0.004616 
    ## [100]    train-error:0.004601 
    ## [101]    train-error:0.004583 
    ## [102]    train-error:0.004567 
    ## [103]    train-error:0.004563 
    ## [104]    train-error:0.004558 
    ## [105]    train-error:0.004558 
    ## [106]    train-error:0.004547 
    ## [107]    train-error:0.004539 
    ## [108]    train-error:0.004519 
    ## [109]    train-error:0.004508 
    ## [110]    train-error:0.004510 
    ## [111]    train-error:0.004505 
    ## [112]    train-error:0.004496 
    ## [113]    train-error:0.004488 
    ## [114]    train-error:0.004481 
    ## [115]    train-error:0.004488 
    ## [116]    train-error:0.004477 
    ## [117]    train-error:0.004474 
    ## [118]    train-error:0.004472 
    ## [119]    train-error:0.004463 
    ## [120]    train-error:0.004461 
    ## [121]    train-error:0.004463 
    ## [122]    train-error:0.004457 
    ## [123]    train-error:0.004454 
    ## [124]    train-error:0.004457 
    ## [125]    train-error:0.004454 
    ## [126]    train-error:0.004446 
    ## [127]    train-error:0.004437 
    ## [128]    train-error:0.004419 
    ## [129]    train-error:0.004414 
    ## [130]    train-error:0.004408 
    ## [131]    train-error:0.004401 
    ## [132]    train-error:0.004399 
    ## [133]    train-error:0.004388 
    ## [134]    train-error:0.004368 
    ## [135]    train-error:0.004361 
    ## [136]    train-error:0.004355 
    ## [137]    train-error:0.004346 
    ## [138]    train-error:0.004344 
    ## [139]    train-error:0.004346 
    ## [140]    train-error:0.004339 
    ## [141]    train-error:0.004328 
    ## [142]    train-error:0.004324 
    ## [143]    train-error:0.004321 
    ## [144]    train-error:0.004299 
    ## [145]    train-error:0.004297 
    ## [146]    train-error:0.004273 
    ## [147]    train-error:0.004268 
    ## [148]    train-error:0.004262 
    ## [149]    train-error:0.004262 
    ## [150]    train-error:0.004259 
    ## [151]    train-error:0.004246 
    ## [152]    train-error:0.004240 
    ## [153]    train-error:0.004235 
    ## [154]    train-error:0.004233 
    ## [155]    train-error:0.004233 
    ## [156]    train-error:0.004235 
    ## [157]    train-error:0.004233 
    ## [158]    train-error:0.004231 
    ## [159]    train-error:0.004222 
    ## [160]    train-error:0.004200 
    ## [161]    train-error:0.004195 
    ## [162]    train-error:0.004195 
    ## [163]    train-error:0.004189 
    ## [164]    train-error:0.004186 
    ## [165]    train-error:0.004169 
    ## [166]    train-error:0.004160 
    ## [167]    train-error:0.004155 
    ## [168]    train-error:0.004155 
    ## [169]    train-error:0.004151 
    ## [170]    train-error:0.004144 
    ## [171]    train-error:0.004138 
    ## [172]    train-error:0.004138 
    ## [173]    train-error:0.004140 
    ## [174]    train-error:0.004131 
    ## [175]    train-error:0.004127 
    ## [176]    train-error:0.004124 
    ## [177]    train-error:0.004124 
    ## [178]    train-error:0.004113 
    ## [179]    train-error:0.004102 
    ## [180]    train-error:0.004093 
    ## [181]    train-error:0.004087 
    ## [182]    train-error:0.004076 
    ## [183]    train-error:0.004069 
    ## [184]    train-error:0.004065 
    ## [185]    train-error:0.004062 
    ## [186]    train-error:0.004058 
    ## [187]    train-error:0.004056 
    ## [188]    train-error:0.004047 
    ## [189]    train-error:0.004045 
    ## [190]    train-error:0.004036 
    ## [191]    train-error:0.004031 
    ## [192]    train-error:0.004036 
    ## [193]    train-error:0.004038 
    ## [194]    train-error:0.004040 
    ## [195]    train-error:0.004038 
    ## [196]    train-error:0.004031 
    ## [197]    train-error:0.004029 
    ## [198]    train-error:0.004009 
    ## [199]    train-error:0.004009 
    ## [200]    train-error:0.004009

``` r
# Identifying important features
importance <- xgb.importance(feature_names = colnames(sparse_matrix), model = bst)
importanceRaw <- xgb.importance(feature_names = colnames(sparse_matrix), model = bst, data = sparse_matrix, label = output_vector)
```

    ## Warning in xgb.importance(feature_names = colnames(sparse_matrix), model
    ## = bst, : xgb.importance: parameters 'data', 'label' and 'target' are
    ## deprecated

``` r
importanceClean <- importanceRaw[,`:=`(Cover=NULL, Frequency=NULL)]

# Plotting important variables
xgb.plot.importance(importance_matrix = importance)
```

![](H1B_Analysis_files/figure-markdown_github/unnamed-chunk-19-1.png)

The

``` r
y_pred <- predict(bst, data.matrix(sparse_matrix_test))

# Attaching predicted column to the test dataset
test_data$pred <- y_pred

# Assigning threshold as 0.5 to convert numeric predictions to categorical
test_data$predv[which(test_data$pred >=0.05)]= 'DENIED'
test_data$predv[which(test_data$pred <0.05)]= 'CERTIFIED'

#TESTING
#Generating confusion matrix
confusion_matrix <- table(test_data$status_final,test_data$predv)
confusion_matrix # 30.182% error *Best model*
```

    ##            
    ##             CERTIFIED DENIED
    ##   CERTIFIED     48838    691
    ##   DENIED          182    453

``` r
FP_error <- confusion_matrix[2,1]/(confusion_matrix[2,2]+confusion_matrix[2,1])
TN_error <- confusion_matrix[1,2]/(confusion_matrix[1,1]+confusion_matrix[1,2])

FP_error_rate <-FP_error *100
TN_error_rate <-TN_error *100

cat("False positive rate is: ", FP_error_rate, "%")
```

    ## False positive rate is:  28.66142 %

``` r
cat("True negative rate is: ", TN_error_rate, "%")
```

    ## True negative rate is:  1.395142 %

``` r
options(na.action=previous_na_action$na.action)
```
