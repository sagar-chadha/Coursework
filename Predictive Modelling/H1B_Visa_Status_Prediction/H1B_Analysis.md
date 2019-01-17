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

The predictors are as follows - <br>

-   `sub_mon` is the month of submission of the visa application.
-   `de_mon` is the month the decision was provided.
-   `workers` is the standardized number of workers with H1B already
    working for the employer.
-   `fulltime` denotes whether the job is full time or not.
-   `pay_unit` is the job one that pays yearly, monthly, hourly or
    weekly.
-   `h1bdepen` signifies whether the candidate has dependents.
-   `willful_violator` whether the employer has, in the past, violated
    H1B rules
-   `status` is the target variable - ‘Certified’ or ‘Denied’
-   `STEM` whether the employment comes under the STEM category
-   `employer_region`, `worksite_region` region of the employer
    headquarters and region of employment
-   `sub_to_dec` scaled time interval between submission of application
    to decision
-   `wage` standardised pay scale of the employment
-   `soc_new` denotes a group name for the type of employment

Exploratory Data Analysis
-------------------------

Let’s look through the data and see if we find any patterns that can
help us predict the outcome.

### Status v/s Waiting time between submission and decision

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-3-1.png)

There is a clear pattern here - applications that wait longer are
generally certified. Decisions that are quickly made are generally
‘Denials’

### Status v/s Wages

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-4-1.png)

We don’t find much of a pattern here. Both categories have wages that
look pretty similar in distribution.

### Status v/s application submission month

![]()

We find denial rates changing slightly with application submission
months but not much.

### decision month vs denial rate

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-6-1.png)

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

A summary of the model shows - <br>

-   Positive coefficients on months 2 and 3 which means odds of denial
    increase in these months.
-   Suprisingly full time jobs also have slightly more odds of denial
-   pay unit ‘year’ has a large negative coefficient i.e. reduces odds
    of denial

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

    ## Confusion matrix for stepwise logistic Regression -

    ##            preds.log.step
    ##             CERTIFIED DENIED
    ##   CERTIFIED     49518     11
    ##   DENIED          423    212

    ## 
    ##  False Negative Rate:

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

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-12-1.png)

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

# Fit LDA model
m_lda=lda(status~.,data=ds.train) 

lda.pred=predict(m_lda,ds.test[,-8])

lda_pred=lda.pred$class

t.lda <- table(ds.test$status, lda_pred)
```

    ## Confusion Matrix for the LDA model -

    ##            lda_pred
    ##             CERTIFIED DENIED
    ##   CERTIFIED     47945   1584
    ##   DENIED          300    335

    ## 
    ##  False negative rate -

    ## 47.24 %

``` r
# Set equal prior probabilities while training the LDA model

m_lda2=lda(status~sub_mon+de_mon+fulltime+pay_unit+h1bdepen
          +willful_violator+STEM+employer_region+worksite_region
          +Sub_to_dec+wage,data=ds.train,prior=c(0.5,0.5))

lda.pred2=predict(m_lda2,ds.test[,-8])
lda_pred2=lda.pred2$class
```

    ## Confusion Matrix for the updated LDA model -

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
is the best number of trees. Next, lets check for the best value for the
threshold.

``` r
# Iterate through different sets of thresholds to find the best set
for (i in seq(0.05, 0.5, 0.05)){
  status.rf.c = randomForest(formula = status ~ ., data = ds.train, ntree = 50,
                           proximity = F, mtry = 4, cutoff=c(i,1-i))
  pred.bag.c = predict(status.rf.c, newdata = ds.test, type = "response" )
  print(table(pred.bag.c,ds.test$status)[2,2]/(table(pred.bag.c,ds.test$status)[1,2]+ table(pred.bag.c,ds.test$status)[2,2]))
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

The accuracies show that the best threshold is the default threshold of
0.5. Let’s run the best model on our data and see how it does on the
test data.

``` r
# Random forest with tuned ntree and prediction threshold.
status.rf <- randomForest(formula = status ~ ., data=ds.train, ntree=50, proximity=F, mtry = 4)
varImpPlot(status.rf, main="Importance for Random Forest")
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-20-1.png)

``` r
# Bagging model with the 3 best variables as identified by Importance plot
status.bag = randomForest(formula = status ~ wage + Sub_to_dec + soc_new, data=ds.train, ntree=50, proximity=F, mtry = 3)
varImpPlot(status.bag)
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-20-2.png)

    ## Confusion matrix for this model -

    ##            pred.bag
    ##             CERTIFIED DENIED
    ##   CERTIFIED     49432     97
    ##   DENIED          290    345

    ## False negative rate -

    ## 45.67 %

### XG Boost Model

XGBoost only works with data matrix format. Let’s convert our data to
that format and try building a model using XGBoost.

``` r
# XGBoost algorithm for classification
bst <- xgboost(data = sparse_matrix, label = output_vector, max_depth = 10,
               eta = 0.3, nthread = 2, nrounds = 200, objective = "binary:logistic",verbose = 0)

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

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1B_Analysis_files/figure-markdown_github/unnamed-chunk-23-1.png)

The chart above shows the most important predictors for our task - <br>

-   Submission to decision time lag
-   wage offered by company
-   Employment grouo
-   the month of march for decision making
-   dependents on the H1B visa candidate

The confusion matrix for this model is shown below - <br>

    ##            
    ##             CERTIFIED DENIED
    ##   CERTIFIED     48838    691
    ##   DENIED          182    453

    ## False positive rate is:  28.66142 %

    ## True negative rate is:  1.395142 %

We see that the XGBoost model has the lowest false positive rate, which
is why we chose this as the best model for predicting the H1B visa
application status for applicants!
