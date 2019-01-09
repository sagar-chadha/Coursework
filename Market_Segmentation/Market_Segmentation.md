Market Segmentation using K-Means Clustering
--------------------------------------------

Each column represents an area of interest that a sample twitter
follower would have tweeted about during the 7 day observation period.
Each cell in that column is the number of tweets that fell into that
interest area. We have about 7882 users with 36 areas of interest and
one column for uncategorised. A natural question at this point would be
to check what are the areas which had the most number of tweets in this
period.

![](Market_Segmentation_files/figure-markdown_github/unnamed-chunk-2-1.png)

The most tweets fall into the chatter category which doesnt tell us a
lot about the audience. However, we do see many tweets about health,
cooking, gaming, photo sharing, fitness and university which sort of
hints towards a fitness savy, mostly young twitter following. We have to
check if there exists a cluster of interests between the users.

A correlation plot between these columns (interest areas) would be
interesting to look at, since a positive high correlation between two
columns would mean that multiple users who tweet about the one thing
also tweet about another. This will lead to insights about our potential
target audience.

![](Market_Segmentation_files/figure-markdown_github/unnamed-chunk-3-1.png)

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

### Deciding the number of clusters

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

![](Market_Segmentation_files/figure-markdown_github/unnamed-chunk-4-1.png)

The plot above shows a continuous decline in RMSE with cluster number.
Let’s choose the value of number of clusters as 6 where the hypothetical
elbow exists and create clusters.

### Results

![Cluster
Analysis.](Chadha_Sagar_files/figure-markdown_github/Cluster%20Analysis.PNG)

The picture above shows the result of the cluster analysis on the data.
The colors indicate whether an interest is dominant(green) or
latent(red). The columns refer to different variables and each row below
the variable name is a different cluster. Row 1 is cluster 1, row 2 is
cluster 2, and so on. A glance at the correlation plot and the cluster
analysis shows that the main followers of the company are - <br>

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
