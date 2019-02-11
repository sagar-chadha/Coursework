Problem 1
---------

Use definitions to prove that
-----------------------------

(*A*<sup> − 1</sup>)<sup>*T*</sup> = (*A*<sup>*T*</sup>)<sup> − 1</sup>

Solution 1
----------

By definition,<br>
*A* \* *A*<sup> − 1</sup> = *I*

Taking the transpose of both sides of the equation we get <br>
(*A* \* *A*<sup> − 1</sup>)<sup>*T*</sup> = *I*<sup>*T*</sup>

simplifying which we get <br>
(*A*<sup> − 1</sup>)<sup>*T*</sup>*A*<sup>*T*</sup> = *I*

 Now, multiplying both sides of the equality by
(*A*<sup>*T*</sup>)<sup> − 1</sup> we get <br>
(*A*<sup> − 1</sup>)<sup>*T*</sup>*A*<sup>*T*</sup>(*A*<sup>*T*</sup>)<sup> − 1</sup> = *I*(*A*<sup>*T*</sup>)<sup> − 1</sup>

 Since
*A*<sup>*T*</sup> \* (*A*<sup>*T*</sup>)<sup> − 1</sup> = *I*

we get the desired proof - <br>
(*A*<sup> − 1</sup>)<sup>*T*</sup> = (*A*<sup>*T*</sup>)<sup> − 1</sup>

Problem 2
---------

A bank makes four kinds of loans to its customers, and these loans yield
the following annual interest rates to the bank: . First mortgage 14% .
Second mortgage 20% . Home improvement 20% . Personal overdraft 10%

We are interested in the bank’s lending strategy. The information we
know is as following: 1. In total $250 million is lent out. <br> 2.
First mortgages are 55% of all mortgages (i.e., first and second
mortgage) issued. <br> 3. Second mortgages are 25% of all loans issued.
<br> 4. The average interest rate on all loans is 15%. <br>

Calculate the lending strategy using matrix inversion.

Solution - <br>
---------------

Choose
*x*<sub>1</sub>, *x*<sub>2</sub>, *x*<sub>3</sub>, *x*<sub>4</sub>
 <br> where these represent investments in first mortgage, second
mortgage, home improvement and personal overdraft respectively. <br>

These are subject to the constraints given below - <br>

1.  In total $250 million is lent out. <br>
    *x*<sub>1</sub> + *x*<sub>2</sub> + *x*<sub>3</sub> + *x*<sub>4</sub> = 250000000

2.  The average interest rate on all loans is 15%. <br>
    0.14*x*<sub>1</sub> + 0.20*x*<sub>2</sub> + 0.20*x*<sub>3</sub> + 0.10*x*<sub>4</sub> = 37500000

3.  First mortgages are 55% of all mortgages <br>
    0.45*x*<sub>1</sub> − 0.55*x*<sub>2</sub> = 0

4.  Second mortgages are 25% of all loans issued. <br>
    0.25*x*<sub>1</sub> − 0.75*x*<sub>2</sub> + 0.25*x*<sub>3</sub> + 0.25*x*<sub>4</sub> = 0

In matrix format this can be represented as - <br>
*A* \* *x* = *b*

<br> where A and b are defined below - <br>

``` r
A <- matrix(c(1,1,1,1,0.14,0.20,0.20, 0.10, 0.45, -0.55, 0, 0, 0.25, -0.75, 0.25, 0.25), 4, 4, byrow = TRUE)
b <- matrix(c(250000000, 37500000, 0, 0), 4, 1)

x <- solve(A) %*% b

x
```

    ##          [,1]
    ## [1,] 76388889
    ## [2,] 62500000
    ## [3,] 31944444
    ## [4,] 79166667

Therefore, we need to lend $ 76,388,889 in first mortgage, $ 62,500,000
in second mortgage, $ 31,944,444 in home improvement and $ 79,166,667 in
personal overdraft.

Problem 3
---------

A company manufactures four variants of the same product, and in the
final part of the manufacturing process, there are assembly, polishing
and packing operations. For each variant, the time required for these
operations is shown below (in minutes) as is the profit per unit sold.

|           | Assembly | Polish | Pack | Profit ($) |
|-----------|----------|--------|------|------------|
| Variant 1 | 2        | 3      | 2    | 1.50       |
| Variant 2 | 4        | 2      | 3    | 2.50       |
| Variant 3 | 3        | 3      | 2    | 3.00       |
| Variant 4 | 7        | 4      | 5    | 4.50       |

Given the current state of the labor force the company estimate that
they have 100000 minutes of assembly time, 50000 minutes of polishing
time and 60000 minutes of packing time available.

Add the non-negative constraints which ensure a positive number of units
manufactured for each type of variant. Formulate this as a Linear
Programming problem with appropriate decision variables, constraints,
and an objective. Do not solve the program.

Solution 3 <br>
---------------

The decision variables in this problem are the counts of each variant
that we decide to make. Let these be called <br>

*x*<sub>1</sub>, *x*<sub>2</sub>, *x*<sub>3</sub>, *x*<sub>4</sub>
 <br> where these represent the units of variant 1,2, 3 and 4
respectively.

These are subject to the constraints - <br>

1.  Non-negative constraints - <br>
    *x*<sub>1</sub>, *x*<sub>2</sub>, *x*<sub>3</sub>.*x*<sub>4</sub> &gt;  = 0
     <br>

2.  Constraints around Assembly time - <br>
    2*x*<sub>1</sub> + 4*x*<sub>2</sub> + 3*x*<sub>3</sub> + 7*x*<sub>4</sub> &gt;  = 100000
     <br>

3.  Constraints around Polish time - <br>
    3*x*<sub>1</sub> + 2*x*<sub>2</sub> + 3*x*<sub>3</sub> + 4*x*<sub>4</sub> &gt;  = 50000
     <br>

4.  Constraints around packing time - <br>
    2*x*<sub>1</sub> + 3*x*<sub>2</sub> + 2*x*<sub>3</sub> + 5*x*<sub>4</sub> &gt;  = 60000

### Objective Function - To maximise <br>

1.5*x*<sub>1</sub> + 2.5*x*<sub>2</sub> + 3*x*<sub>3</sub> + 4.5*x*<sub>4</sub>

Problem 4
---------

### Part a

Use “for loop(s)” and “if statement” within the loop to generate a 20 by
20 Lehmer matrix A where

A<sub>i,j</sub> = i/j, j &gt;= i

and

A<sub>i,j</sub> = j/i, j &lt; i

Solution a)
-----------

``` r
A <- matrix(rep(0, 400), 20, 20, byrow = TRUE)

for (i in seq(1, 20)){
  for (j in seq(1, 20)){
    if (j >= i){
      A[i, j] = i/j
    }else{
      A[i, j] = j/i
    }
  }
}
```

### Part b

Test whether A is symmetric or not. Namely, is
*A* = *A*<sup>*T*</sup>

### Solution b)

``` r
A_transpose <- t(A)
identical(A, A_transpose)
```

    ## [1] TRUE

The answer is `TRUE` which means that A and the transpose of A are
equal. Therefore, A is symmetric.

### Part c)

Calculate
*C* = *A*<sup> − 1</sup>

in R. Test whether the inverse is correct. That is, calculate C × A in R
and see whether the product is an identity matrix.

### Solution c)

``` r
C <- solve(A) # take the inverse of A

# check whether product is Identity matrix
Q <- A %*% C

# check equality with diagonal matrix
D <- diag(nrow = 20, ncol = 20)

all.equal(Q, D)
```

    ## [1] TRUE

The `TRUE` shows that the result is in fact an identity matrix!

### Part d)

Assign \[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1\]
to d in R.

### Solution d)

``` r
d <- c(seq(1:10), 10:1)
d
```

    ##  [1]  1  2  3  4  5  6  7  8  9 10 10  9  8  7  6  5  4  3  2  1

### Part e)

Solve for x in the equation Ax = Cd.

### Solution e)

The equation

*A* \* *x* = *C* \* *d*

translates to

*x* = (*A*<sup> − 1</sup>)*C**d*

Let’s solve this using R -

``` r
# convert d to a matrix of 20X1
d <- matrix(d, 20, 1)

x <- solve(A, (C %*% d))

x
```

    ##                [,1]
    ##  [1,] -3.404684e-15
    ##  [2,]  9.118632e-15
    ##  [3,]  1.652012e-14
    ##  [4,] -4.618528e-14
    ##  [5,]  2.220446e-14
    ##  [6,] -2.295600e-14
    ##  [7,]  7.320412e-14
    ##  [8,] -5.305386e-14
    ##  [9,] -2.481203e+01
    ## [10,]  2.006424e+01
    ## [11,]  3.581375e+01
    ## [12,] -3.006263e+01
    ## [13,] -3.736996e-04
    ## [14,] -2.772044e-04
    ## [15,] -2.099688e-04
    ## [16,] -1.619541e-04
    ## [17,] -1.269228e-04
    ## [18,] -1.008779e-04
    ## [19,]  9.505933e+01
    ## [20,] -1.000629e+02
