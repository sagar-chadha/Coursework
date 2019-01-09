Market Segmentation using K-Means Clustering
--------------------------------------------

Let’s read in our data and see what it looks like.

    ## corrplot 0.84 loaded

    ##        User chatter current_events travel photo_sharing uncategorized
    ## 1 hmjoe4g3k       2              0      2             2             2
    ## 2 clk1m5w8s       3              3      2             1             1
    ## 3 jcsovtak3       6              3      4             3             1
    ## 4 3oeb4hiln       1              5      2             2             0
    ## 5 fd75x1vgk       5              2      0             6             1
    ## 6 h6nvj91yp       6              4      2             7             0

### Data Specifics

-   Each column represents an area of interest that a sample twitter
    follower would have tweeted about during the 7 day observation
    period.
-   Each cell in that column is the number of tweets that fell into that
    interest area.
-   We have about 7882 users with 36 areas of interest and one column
    for uncategorised.

### What are the areas with the most number of tweets in this period ?

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Market_Segmentation_files/figure-markdown_github/unnamed-chunk-2-1.png)

The most tweets fall into the chatter category which doesnt tell us a
lot about the audience. However, we do see many tweets about *health,
cooking, gaming, photo sharing, fitness* and *university* which sort of
hints towards a fitness savy, mostly young twitter following. We have to
check if there exists a cluster of interests between the users.

### Correlations between interest areas

A correlation plot between these columns (interest areas) would be
interesting to look at, since a positive high correlation between two
columns would mean that multiple users who tweet about the one thing
also tweet about another. This will lead to insights about our potential
target audience.

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Market_Segmentation_files/figure-markdown_github/unnamed-chunk-3-1.png)

The blue squares above are at the intersection of interest areas that
are positively correlated. We see some dark blue squares at the
intersection of the following interests - <br>

1.  Politics, travel, news, computers
2.  health, personal fitness, outdoors
3.  Religion, food, family, school, parenting
4.  Fashion, beauty, cooking

These interest intersections are beginning to tell us something about
our followers. Let’s try to cluster the tweets and see if the results
match.

Clustering users to get market segments
---------------------------------------

Clustering for this exercise means grouping together users who would
tweet about similar types of things, see what those things are and try
to make sense of what kind of people are following the brand on Twitter.

### Clustering Strategy

We can cluster using the raw counts of tweets or a proportion of tweet
counts (the proportion of all tweets by a user that were for a
particular interest area) depending on the application. I believe that
the clusters should have people who actually use the twitter platform
and are very active on it, which is why I would use raw counts as the
basis for my clustering.

### Deciding the number of clusters

The number of clusters to be formed can be done both mathematically and
intuitively depending on the business’ appetite for different customer
strategies. If a business can only manage 3 strategies at once then 3 is
the correct number of strategies. Here, devoid of business inputs
however, I will use the mathematical way to determine an optimum number
of strategies

``` r
# Lets use clustering to see if we can get any new insights

num_centers <- c(2:18)
rmse_all <- NULL

for(num_center in num_centers){
  cluster <- kmeans(X, centers = num_center, nstart = 50, iter.max = 20)
  rmse <- sqrt(mean(cluster$withinss))
  rmse_all <- c(rmse_all, rmse)
}

# Lets plot the RMSE with the number of clusters chosen

plot(num_centers, rmse_all, type = 'b',
     xlab = "Number of Centers", ylab = "RMSE", main = "RMSE vs Number of Clusters")
```

![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Market_Segmentation_files/figure-markdown_github/unnamed-chunk-4-1.png)

The plot above shows a continuous decline in RMSE with cluster number.
RMSE captures the square root of the L2 distance of each cluster point
from its centroid. Ideally for clustering, we want that clusters be
homogenous within and heterogenous without. Let’s choose the value of
number of clusters as 6 where the elbow exists and create clusters.

### Results

![Cluster Analysis.](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Market_Segmentation_files/figure-markdown_github/Cluster%20Analysis.PNG)

The picture above shows the result of the cluster analysis on the data.
The colors indicate whether an interest is dominant(green) or
latent(red). The columns refer to different interest areas and each row
below the variable name is a different cluster. Row 1 is cluster 1, row
2 is cluster 2, and so on. A glance at the correlation plot and the
cluster analysis shows that the main followers of the company are - <br>

1.  **Undergraduate Students** - (Cluster 1) There is a significant
    number of college students who tweet about their university life and
    the games that they play online. This represents a very young
    demographic, mostly undergraduate students.

2.  **Working women** - (Cluster 2) Correlation between cooking, fashion
    and beauty most closely relates itself with the life of working
    women who would share beauty tips, healthy cooking advice and work
    fashion.

3.  **TV news anchors** - (cluster 4) Correlations between computer,
    travel and politics. are possibly TV news anchors who travel around
    the world for news coverage and report on the changing political
    landscape.

4.  **Fitness Geeks/ Health Practitioners** - (Cluster 5) Many posts
    about the personal fitness, outdoors and nutrition mean these are
    either fitness geeks or health practitioners who are promoting a
    better lifestyle.

5.  **Middle aged Parents** - (Cluster 6) We see tweets about religion,
    school, parenting and family. These should be mostly middle aged
    parents with expendable income.
