Let’s first load the data from the .Rdata file provided.

``` r
load('data.Rdata')
```

Lets run the LASSO method first and look at the coefficients - <br>

``` r
set.seed(10)
cv_model <- cv.glmnet(X,y,type.measure="mse", alpha=1)
best_lambda <- cv_model$lambda.min
lasso <- glmnet(X,y, alpha=1, lambda=best_lambda)
lasso_coeff <- lasso$beta
```

We see that the maximum absolute value of the coefficients is - <br>

    ## [1] 1.018396

This helps us think about the values of M to be chosen for our problem.

### MIQP formulation

We need to minimize the sum of squared errors - <br>

$$
\\frac{1}{2}\\sum\_{i}(y - X\\beta)^2
$$
 which can be written as - <br>

$$
\\frac{1}{2}(y-X\\beta)^T(y - X\\beta)
$$
 This can further be simplified to - <br>

$$
\\frac{1}{2}(y^Ty - 2y^TX\\beta + \\beta^TX^TX\\beta)
$$
 The above equation resembles whats required for the gurobi formulation
of our problem.

The constraints for our problem are - <br>

∑*z*<sub>*i*</sub> ≤ *k*

 − *M**z*<sub>*i*</sub> − *β*<sub>*i*</sub> ≤ 0

*β*<sub>*i*</sub> − *M**z*<sub>*i*</sub> ≤ 0

Before we formulate the problem in R, we need to define a function that
takes as inputs the M, k, X and y values and returns all the model
inputs. This is done below - <br>

``` r
X1 <- cbind(rep(1,500), X)

gurobi_model_builder <- function(y, X, M, k){
  
  X1 <- cbind(rep(1,500), X)
  
  ## set Q
  Q = matrix(0, 129,129)
  for (i in 1:65){
    for (j in 1:65){
      if (i==j){
        Q[i,i]=sum(0.5*X1[,i]^2)
      }
      else{
        Q[i,j]=sum(X1[,i]*X1[,j])/2
        Q[j,i]=sum(X1[,i]*X1[,j])/2
      }
    }
  }
  
  ## set c
  c=c() #inital value
  for (i in 1:65){
    temp = 0
    for (j in 1:500){
      temp= temp+y[j]*sum(X1[j,i])
    }
    c=c(c, -temp)
  }
  # include z1 to z64 here
  c = c(c, rep(0, 64))
  
  # set alpha
  alpha = sum(y^2)
  
  ## set A, b, sense
  ### beta_{i} smaller than M*Z_{i} & beta_{i} bigger than -M*Z_{i}
  A=cbind(rep(0, 129), rbind(cbind(diag(64), -M*diag(64)), cbind(diag(64), M*diag(64)),c(rep(0,64), rep(1,64))))
  b=c(rep(0, 128), k)
  sense=c(rep('<=', 64), rep('>=', 64), '<=')
  
  
  model=list()
  model$Q=Q
  model$obj=c
  model$A=A
  model$rhs=b
  model$vtype = c(rep('C', 65), rep('B', 64))
  model$sense = sense
  
  return(model)
}
```

Now that we have the model, we need to write a loop which continues
until all betas obtained in the result are less than the chosen value of
M - &lt;br

``` r
# we will use all values of M starting 0.1
M <- 0.05

# set the value of k
k <- 8

beta_answer <- c(M, rep(0, 128))

while(any(M == abs(beta_answer))){
  
  M = 2*M
  
  model <- gurobi_model_builder(y, X, M, k)
  
  params <- list(OutputFlag = 0)
  
  results <- gurobi(model, params = params)
  
  beta_answer <- results$x[1:65]
}
```

    ## Chosen variables are: V1 V9 V17 V25 V33 V41 V49 V57

### Prediction error for lasso

Using the formula for prediction error given in the problem statement,
we get the prediction error for lasso as - <br>

    ## Prediction error for Lasso 0.00671972

### Prediction error for MIQP

    ## Prediction error for MIQP 0.004456055

We see that the prediction error for MIQP is 0.0044 as compared to
0.0067 for that of lasso!
