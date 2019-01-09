Grocery Store Purchase Analysis using Association Rules
-------------------------------------------------------

Reading in the data, we find that the data has 9,835 transactions. Each
row is one transaction and each comma separated item is one item from
that transaction!

    ## [1] 9835

Let’s get a sense of what the customers are buying from this store! This
will lead into our analysis of what items are most frequently bought
together.

What is the number of different items sold here?

    ## [1] 169

What sells most commonly?

![](Association_Rules_files/figure-markdown_github/unnamed-chunk-5-1.png)

The most commonly sold item is **Whole Milk** followed by **Vegetables**
and **Rolls/Buns**! Out of the 169 unique items sold, we see that only
15 odd items are sold in considerable amounts. There is a long tail of
low selling items! What are these?

    ##              item_vector Freq
    ## 1              baby food    1
    ## 2   sound storage medium    1
    ## 3  preservation products    2
    ## 4                   bags    4
    ## 5        kitchen utensil    4
    ## 6         baby cosmetics    6
    ## 7         frozen chicken    6
    ## 8         toilet cleaner    7
    ## 9        make up remover    8
    ## 10        salad dressing    8

Let’s see what items are associated with other items on the grocery
list? This would mean items that are most commonly sold together in a
transaction! We will use a low *minsup* threshold here since we want
item associations that would not be obvious to the grocer at first
glance- Items like milk, eggs, vegetables are the most commonly bought
items and most people know about their association

    ## Apriori
    ## 
    ## Parameter specification:
    ##  confidence minval smax arem  aval originalSupport maxtime support minlen
    ##         0.5    0.1    1 none FALSE            TRUE       5   0.001      1
    ##  maxlen target   ext
    ##      10  rules FALSE
    ## 
    ## Algorithmic control:
    ##  filter tree heap memopt load sort verbose
    ##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
    ## 
    ## Absolute minimum support count: 9 
    ## 
    ## set item appearances ...[0 item(s)] done [0.00s].
    ## set transactions ...[169 item(s), 9835 transaction(s)] done [0.00s].
    ## sorting and recoding items ... [157 item(s)] done [0.00s].
    ## creating transaction tree ... done [0.00s].
    ## checking subsets of size 1 2 3 4 5 6 done [0.01s].
    ## writing ... [5668 rule(s)] done [0.00s].
    ## creating S4 object  ... done [0.00s].

    ## set of 10 rules

![](Association_Rules_files/figure-markdown_github/unnamed-chunk-7-1.png)

A study of the associations shows us the following - <br>

1.  People purchase soda, popcorn and other salty snacks together.
2.  Cheese, ham, white bread and eggs usually sell together.
3.  Sugar, baking powder and flour sell together, these are usually
    baking items.
4.  Cheese, curd, whipped cream and yogurt sell together!
