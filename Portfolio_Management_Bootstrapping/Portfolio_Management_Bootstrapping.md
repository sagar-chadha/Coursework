Finding the optimal investment Strategy using Bootstrapping
----------------------------------------------------------------

Let’s extract the prices for each asset class using the getSymbols
function and adjust for splits/dividends.

The prices are daily starting from 3rd January 2007. To get an idea of
the kind of returns that they have provided, let’s calculate the percent
changes between two closing prices of each of these assets.

To get a sense of the risk-returns properties of these assets, we can
look at some summary statistics for these funds -

    ##    ClCl.SPYa          ClCl.TLTa          ClCl.LQDa       
    ##  Min.   :-0.09845   Min.   :-0.05045   Min.   :-0.09111  
    ##  1st Qu.:-0.00390   1st Qu.:-0.00512   1st Qu.:-0.00190  
    ##  Median : 0.00064   Median : 0.00054   Median : 0.00038  
    ##  Mean   : 0.00036   Mean   : 0.00027   Mean   : 0.00020  
    ##  3rd Qu.: 0.00561   3rd Qu.: 0.00563   3rd Qu.: 0.00243  
    ##  Max.   : 0.14520   Max.   : 0.05166   Max.   : 0.09768  
    ##    ClCl.EEMa         ClCl.VNQa       
    ##  Min.   :-0.6790   Min.   :-0.19514  
    ##  1st Qu.:-0.0086   1st Qu.:-0.00689  
    ##  Median : 0.0008   Median : 0.00066  
    ##  Mean   : 0.0001   Mean   : 0.00039  
    ##  3rd Qu.: 0.0092   3rd Qu.: 0.00773  
    ##  Max.   : 0.2277   Max.   : 0.17007

The summary statistics show that the least risky investment would be one
in US Treasury bonds, since the min-max variation is the least in those.
The most risky investment is in Emerging market equities. They vary the
most - from -67% to +23%!

Let’s now plot the closing prices along with returns for all possible
investments on a graph to see if we have captured the good and bad
phases of the stock market -

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Bootstrapping_files/figure-markdown_github/unnamed-chunk-4-1.png)

The chart shows that we have enough data from the past to capture the
ups and downs in the stock market.

### Strategy one - Even split of my assets

An even split of my assets strategy is pretty straightforward, 20%
across all portfolios. Let’s take 5000 bootstrap samples from the past
returns on each of these investments to make an informed judgement about
the future returns in 20 days.

Here, we assume that we re-distribute the total capital at the end of
each day equally amongst the five ETFs.

``` r
set.seed(99)
wealth_matrix_1 <- matrix(0, nrow = 5000, ncol = 20)

for(i in 1:5000){
  
  wealth_total <- 100000

  for (j in 1:20){
    
    wealth <- wealth_total
    
    split_1 <- c(0.2, 0.2, 0.2, 0.2, 0.2)
  
    wealth_split_1 <- wealth*split_1
  
    sample_day <- mosaic::resample(all_returns, 1, orig.ids = FALSE)
  
    sample_return <- wealth_split_1 + wealth_split_1* sample_day
    
    wealth_total <- sum(sample_return)
  
    wealth_matrix_1[i,j] <- wealth_total
  }
}

# plot the profit distribution
ggplot(mapping = aes(wealth_matrix_1[,20] - 100000)) +
  geom_histogram(bins = 20, color = 'black', fill = 'white') +
  theme_minimal() +
  labs(x = "Net Profit",
       y = "Frequency",
       title = "Risk of Loss",
       subtitle = "Strategy 1")
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Bootstrapping_files/figure-markdown_github/unnamed-chunk-5-1.png)

The above histogram shows the difference between final amount at the end
of 20 days and the initial wealth invested. Negtives mean losses and
positive means profits. The value at risk at the 5% level is $
-6581.1531

### Strategy two - Safer than even split

From the summary statistics that we saw, we notice that *SPY*, *TLT* and
*LQD* are the safe bets. They have not had shocking upswings but
correspondingly they have not had large downswings.

``` r
set.seed(99)

wealth_matrix_2 <- matrix(0, nrow = 5000, ncol = 20)

for(i in 1:5000){
  
  wealth_total <- 100000

  for (j in 1:20){
    
    wealth <- wealth_total
    
    split_2 <- c(0.2, 0.4, 0.4, 0, 0)
  
    wealth_split_2 <- wealth*split_2
  
    sample_day <- mosaic::resample(all_returns, 1, orig.ids = FALSE)
  
    sample_return <- wealth_split_2 + wealth_split_2* sample_day
    
    wealth_total <- sum(sample_return)
  
    wealth_matrix_2[i,j] <- wealth_total
  }
}

# plot the profit distribution
ggplot(mapping = aes(wealth_matrix_2[,20] - 100000)) +
  geom_histogram(bins = 20, color = 'black', fill = 'white') +
  theme_minimal() +
  labs(x = "Net Profit",
       y = "Frequency",
       title = "Risk of Loss",
       subtitle = "Strategy 2")
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Bootstrapping_files/figure-markdown_github/unnamed-chunk-6-1.png)

The above histogram shows the difference between final amount at the end
of 20 days and the initial wealth invested. Negtives mean losses and
positive means profits. The value at risk at the 5% level is $
-2921.8683

### Strategy three - Aggressive strategy

Similarly, we notice that *VNQ* and *EEM* are the riskier portfolios due
to their volatile nature. We invest more in EEMs due to the possibility
of a very large return on my investment.

``` r
set.seed(99)
wealth_matrix_3 <- matrix(0, nrow = 5000, ncol = 20)

for(i in 1:5000){
  
  wealth_total <- 100000

  for (j in 1:20){
    
    wealth <- wealth_total
    
    split_3 <- c(0, 0, 0, 0.5, 0.5)
  
    wealth_split_3 <- wealth*split_3
  
    sample_day <- mosaic::resample(all_returns, 1, orig.ids = FALSE)
  
    sample_return <- wealth_split_3 + wealth_split_3* sample_day
    
    wealth_total <- sum(sample_return)
  
    wealth_matrix_3[i,j] <- wealth_total
  }
}

# plot the profit distribution
ggplot(mapping = aes(wealth_matrix_3[,20] - 100000)) +
  geom_histogram(bins = 20, color = 'black', fill = 'white') +
  theme_minimal() +
  labs(x = "Net Profit",
       y = "Frequency",
       title = "Risk of Loss",
       subtitle = "Strategy 3")
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Bootstrapping_files/figure-markdown_github/unnamed-chunk-7-1.png)

The above histogram shows the difference between final amount at the end
of 20 days and the initial wealth invested. Negtives mean losses and
positive means profits. The value at risk at the 5% level is $
-13103.2045

Looking at the bootstrap resamples and the related value at risk at 5%,
we see that - <br>

1.  The strategy to **divide assets amongst the portfolios equally** has
    a value at risk at 5% of $-6581.1531. However, looking at the other
    summary statistics shows that -

<!-- -->

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   78874   97815  100422  100467  103150  120280

We would end up with around USD 100467 on average with a possibility to
even reach USD 120280!

1.  The strategy to **play safe and invest only in SPY, TLT and LQD**
    has a value at risk at 5% of $-2921.8683. Looking at the other
    summary statistics, see that -

<!-- -->

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   92160   99117  100498  100538  101956  109821

The strategy to play safer shows in the results. On average we end up
with around USD 100538 and the max we can possibly make is USD 109821.

1.  The strategy to **play aggressively and invest only in EEM and VNQ**
    has a value at risk at 5% of $-13103.2045. Looking at the other
    summary statistics, we see that -

<!-- -->

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   54037   95129  100240  100345  105624  141394

There is a super high risk with this investment. Although the average is
still around USD 100345, we can possible more than double our money and
end up with USD 141394 or lose a lot and end up with just USD 54037.

### Conclusion
The investor has to carefully regard these results to make his
decision - <br> 1. An old investor should play very safely and choose
strategy 2. <br> 2. A younger investor with a good income can possibly
take a chance with strategy 3 and try his luck!**
