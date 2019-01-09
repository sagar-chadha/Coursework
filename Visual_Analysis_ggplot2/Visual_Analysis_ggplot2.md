Green Buildings: Are they worth the investment?
-----------------------------------------------

Reading in the green buildings data, we find that the dataset has 7,894
rows (buildings) and 23 columns.

Let’s look at the columns that our data has -

    ## Observations: 7,894
    ## Variables: 23
    ## $ CS_PropertyID     <chr> "379105", "122151", "379839", "94614", "3792...
    ## $ cluster           <int> 1, 1, 1, 1, 1, 1, 6, 6, 6, 6, 6, 6, 6, 6, 6,...
    ## $ size              <dbl> 260300, 67861, 164848, 93372, 174307, 231633...
    ## $ empl_gr           <dbl> 2.22, 2.22, 2.22, 2.22, 2.22, 2.22, 4.01, 4....
    ## $ Rent              <dbl> 38.56, 28.57, 33.31, 35.00, 40.69, 43.16, 12...
    ## $ leasing_rate      <dbl> 91.39, 87.14, 88.94, 97.04, 96.58, 92.74, 94...
    ## $ stories           <int> 14, 5, 13, 13, 16, 14, 11, 15, 31, 21, 11, 1...
    ## $ age               <int> 16, 27, 36, 46, 5, 20, 38, 24, 34, 36, 32, 2...
    ## $ renovated         <int> 0, 0, 1, 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0,...
    ## $ class_a           <int> 1, 0, 0, 0, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1,...
    ## $ class_b           <int> 0, 1, 1, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ LEED              <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ Energystar        <int> 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ green_rating      <int> 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ net               <int> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,...
    ## $ amenities         <int> 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,...
    ## $ cd_total_07       <int> 4988, 4988, 4988, 4988, 4988, 4988, 2746, 27...
    ## $ hd_total07        <int> 58, 58, 58, 58, 58, 58, 1670, 1670, 1670, 16...
    ## $ total_dd_07       <int> 5046, 5046, 5046, 5046, 5046, 5046, 4416, 44...
    ## $ Precipitation     <dbl> 42.57, 42.57, 42.57, 42.57, 42.57, 42.57, 25...
    ## $ Gas_Costs         <dbl> 0.01370000, 0.01373149, 0.01373149, 0.013731...
    ## $ Electricity_Costs <dbl> 0.02900000, 0.02904455, 0.02904455, 0.029044...
    ## $ cluster_rent      <dbl> 36.78, 36.78, 36.78, 36.78, 36.78, 36.78, 17...

What’s with the LEED and Energy Star Ratings? Are they awarded
individually or can a building have both the certifications?

    ##        EnergyStar-0 EnergyStar-1
    ## LEED-0         7209          631
    ## LEED-1           47            7

The crosstab above tells that of the 685 buildings with a green rating,
54 buildings have an LEED rating, 638 have an EnergyStar rating and 7
have both these ratings.

### Missing Values

    ##     CS_PropertyID           cluster              size           empl_gr 
    ##                 0                 0                 0                74 
    ##              Rent      leasing_rate           stories               age 
    ##                 0                 0                 0                 0 
    ##         renovated           class_a           class_b              LEED 
    ##                 0                 0                 0                 0 
    ##        Energystar      green_rating               net         amenities 
    ##                 0                 0                 0                 0 
    ##       cd_total_07        hd_total07       total_dd_07     Precipitation 
    ##                 0                 0                 0                 0 
    ##         Gas_Costs Electricity_Costs      cluster_rent 
    ##                 0                 0                 0

We also see that we have about 74 missing values in the *empl.gr*
column. This represents the year on year growth rate for employment in
the building’s geographic region.

### The Analysis

### Buildings with low occupancy rates. <br>

The first issue raised by the analyst is of some buildings with low
occupancy rates. Do these even exist?

Let’s plot rent with occupancy rates and see where these buildings fall.

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-5-1.png)

Looking at the plot above, we see that there are many buildings with
occupancy rates less than 10% - 215 to be exact.

Hmm, so we see that for buildings with less than 10% occupancy rates,
the rents vary a lot, but the variation is still not clear with this
chart. We also see that the discrepancies are larger for very small
values of the occupancy rate. Let’s zoom in a bit more to see it
clearly.

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-6-1.png)

Much better! The graph now shows clearly that the points below leasing
rates of 1% are the real culprits in being unusual with their rents. We
will remove these points instead to proceed further with the analysis.

We removed around 167 rows this way and are left with 7727 rows in the
data.

### Median rent for green and non-green buildings. <br>

The next issue is that of calculating the rent for green and non-green
buildings respectively. A boxplot is ideal here.

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-8-1.png)

The rent values are marred by outlier values as shown by the large
number of dots extending above the dense group of dots. The median value
of green buildings is USD 27.6 while that of non-green buildings is USD
25. So there is a USD 2.6 difference while taking no variable into
account.

### Problem with the analysis - 1

The problem that the analyst has not thought of is that he cannot
directly compare the rents of buildings quoted on net or non-net since
that means utility is included or not.

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-9-1.png)

The boxplot clearly shows the difference in rents between net and
non-net rent payers as it is expected to be. The analyst cannot
aggregate values disregarding this distinction. Since we need to
consider rent with utilities and there is no way to calculate the
utility costs, we will subset our data where net = 0 for further
analysis.

### Problem with the analysis - 2

The other assumption that the analyst makes is that rent difference
remains between green and non-green buildings continuously for a period
of 30 years. Lets check whether this is true-

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-10-1.png)

So, we see that expected rent for newer buildings (&lt;5 years) is
actually higher for non-green buildings. The rent for green buildings
starts going up and pays back after the 15th year. So the analyst is
incorrect in his break even analysis. Making a green building will - 1)
Cost more upfront by $5 million. 2) Pay less in rent initially than a
non-green building, but then more after 15 years of its life. This can
be attributed to the more sustainable material that means a better
indoor quality than a non-green building.

### Problem with the analysis - 3

The analyst fails to consider the value of amenities in deciding the
rent of the building.
![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-11-1.png)

The graph above shows that given amenities, the difference between the
rent of a green vs a non green building is lower initially.

So, the real estate developer should consider making a green building if
she is looking for a long term economic return on her building. Also,
she is better off with a building with amenities. Assuming these and a
100% occupancy rate, this is how building a non-green building compares
with building a green building -

| Costs          | Non-Green   | Green       |
|----------------|-------------|-------------|
| Construction   | 100,000,000 | 105,000,000 |
| Rent(0-15yrs)  | 107,205,000 | 104,062,500 |
| Rent(16-30yrs) | 104,287,500 | 105,750,000 |
| Rent(31-60yrs) | 191,250,000 | 237,825,000 |

Therefore the developer would recover the additional construction cost
somewhere in the 16th year after construction. **If the developer wants
a quick recovery within 10 years, the best option would be a non-green
building.** However, renting out apartments with rents inclusive of
utilities means that the developer can make money on the utility bills
also. We just dont have that information here to compute the results.

Flights at ABIA - Austin Bergstrom International Airport
--------------------------------------------------------

Let’s read in the dataset. It has data for each flight that comes into
ABIA or goes out of ABIA for the year 2008. We have each flight’s basic
details such as time of departure, arrival, any recorded delays, flight
number, Distance, taxi time, etc. The dimensions of the data are -

    ## [1] 99260    29

Let’s also look at a few rows of the data -

    ##   Year Month DayofMonth DayOfWeek DepTime CRSDepTime
    ## 1 2008     1          1         2     120       1935
    ## 2 2008     1          1         2     555        600
    ## 3 2008     1          1         2     600        600
    ## 4 2008     1          1         2     601        605
    ## 5 2008     1          1         2     601        600
    ## 6 2008     1          1         2     636        645

The variables provided in the dataset lead to a few obvious questions
that can be answered here - <br>

1.  How many flights come into and go out of ABIA on a day, week, month,
    etc.?
2.  What are the most common departure times on any weekday or weekends?
3.  Which flight carriers experience the most delays?
4.  Which destination airports experience the most delays?
5.  Most frequent reason for flight cancellation?

Let’s look at the missing values in the data first, before we begin our
analyses -

    ##          Column Name Missing_Count
    ## 1               Year             0
    ## 2              Month             0
    ## 3         DayofMonth             0
    ## 4          DayOfWeek             0
    ## 5            DepTime          1413
    ## 6         CRSDepTime             0
    ## 7            ArrTime          1567
    ## 8         CRSArrTime             0
    ## 9      UniqueCarrier             0
    ## 10         FlightNum             0
    ## 11           TailNum          1104
    ## 12 ActualElapsedTime          1601
    ## 13    CRSElapsedTime            11
    ## 14           AirTime          1601
    ## 15          ArrDelay          1601
    ## 16          DepDelay          1413
    ## 17            Origin             0
    ## 18              Dest             0
    ## 19          Distance             0
    ## 20            TaxiIn          1567
    ## 21           TaxiOut          1419
    ## 22         Cancelled             0
    ## 23  CancellationCode         97840
    ## 24          Diverted             0
    ## 25      CarrierDelay         79513
    ## 26      WeatherDelay         79513
    ## 27          NASDelay         79513
    ## 28     SecurityDelay         79513
    ## 29 LateAircraftDelay         79513

We find that *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*
and *LateAircraftDelay* have around 80,000 missing values. These
shouldn’t, therefore, provide any meaningful insights about the data.

### What are the most common destinations for flights from Austin?

This question is easily answered using the number of flights from Austin
to each of these destinations. We see through the chart below that
Dallas is the most common destination from Austin! Around 11,000 flights
flew from Austin to DAL and DFW in 2008, this is almost 900 flights a
month!

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-16-1.png)

### Are these specific months when more flights depart from Austin to these destinations?

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-17-1.png)

The chart shows that flights from Austin to all these destinations
remain mostly the same per month, but for DAL the number of flights drop
sharply in July from 600 to 300 odd. Since the data is only for 2008, we
cant say if this a pattern that happens every year. However, we do see
that since July 2008, there is an upward trend for flights to DFW
airport!

### What proportion of flights were cancelled from Austin in 2008?

    ## [1] 1.475122

We see that 1.4% of flights were cancelled from Austin.

#### How many flights were cancelled to Austin?

    ## [1] 1.386063

Slightly fewer- 1.38% flights got cancelled to Austin.

#### What are the most common reasons for cancelled flights coming into Austin?

    ## .
    ##  Carrier  Weather      NAS Security 
    ##      329      311       48        0

So most flights are delayed due to carrier related and weather related
reasons! Sometimes, 48 out of ~700, there are traffic related issues as
well! **Austin is a well managed airport!**

### What does the average delay from Austin look like?

We will look at departure delays from Austin.

    ##     DepDelay      
    ##  Min.   :-36.000  
    ##  1st Qu.: -5.000  
    ##  Median : -1.000  
    ##  Mean   :  7.425  
    ##  3rd Qu.:  5.000  
    ##  Max.   :875.000  
    ##  NA's   :730

So we see that the maximum departure delay is around 875 minutes which
is 15 hours! But we also see negative values here!! These might mean
that a flight got permission to depart before is CRS departure time.
Let’s calculate the mean, median for only rows where delay was greater
than 0.

    ## [1] 11

Flights departing from Austin are delayed by 11 minutes (median).

### Is there a pattern for certain destinations being affected more by delays?

It could be that certain carriers are more inefficient than others in
handling operations, thereby leading to flight delays! Let’s look at the
pattern of delays across UniqueCarriers for departures from Austin.

``` r
# lets see the top 5 destinations with the most departure delays
max_delay_dest <- airline %>%
  filter(Origin == "AUS", Cancelled == 0, DepDelay > 0) %>%
  group_by(Dest) %>%
  summarise("Median_Delay" = median(DepDelay)) %>%
  arrange(-Median_Delay)%>%
  head(5)


max_delay_dest <- as.character(max_delay_dest$Dest)
```

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-24-1.png)

The boxplot shows that there are clearly large outliers in departure
delays for each destination. Let’s zoom in a bit on the top 5
destinations with most departure delays-

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-25-1.png)

In terms of departure delays from Austin we can say that **EWR - Newark
Airport**, **SNA - John Wayne Airport, California**, and **STL - St
Louis Airport Missouri** are the destinations with the most delays! We
will disregard DSM since it just has one point

### What are the worst times to fly for each month from Austin?

Let’s define the worst times to be times which are most prone to delays!
Lets look at proportion of flights delayed for different times during
the day across months and check!

Lets categorise departure times from Austin into - <br>

1.  Early Morning (12am - 6am)
2.  Morning (6am - 9am)
3.  Pre-Afternoon (9am - 12pm)
4.  Affternoon (12pm - 3pm)
5.  Evening (3pm - 6pm)
6.  Night (6pm- 9pm)
7.  Late Night (9pm - 12am)

We will then look at the median departure delays for these times for
each month in 2008.

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-28-1.png)

The heatmap above shows the proportion of delays) darker colors mean
more proportion of delays) by time of day and month. **We see that the
most delays happen in the evening and night time across times. December
is a particularly bad month in terms of delays as we see darker colors
across the time range.**

### Do weekends and weekdays have different patterns of delays?

Let’s define weekdays where *DayOfWeek* is 1 through 4. Friday, Saturday
and Sunday are the weekends.

![](Visual_Analysis_ggplot2_files/figure-markdown_github/unnamed-chunk-30-1.png)

The worst times to fly during weekdays are Evening and Night times in
terms of proportion of delays. We see that Weekends have equal
proportion of delays for the 3-6pm and 6-9pm time slots!

### Conclusion

**We see that the most frequent destinations from Austin are Dallas,
Denver, Phoenix (Arizona)** and the pattern of flights over the year
showed some interesting patterns for DAL airport as the number of
flights per month dropped to 300 from July. We did not find any such
pattern for the other airports.

People planning to fly out from Austin to <br>

1.  **EWR - Newark Airport**
2.  **SNA - John Wayne Airport, California**
3.  **STL - St Louis Airport Missouri** <br>

can expect the most delays! These destinations **have a median flight
delay of around 35 minutes**.

Cancellations are very few at ABIA - 1.4%. **An analysis of these shows
that almost all flights cancelled are due to carrier or weather
reasons!**

In terms of months, **December is a particularly busy month at ABIA and
also the worst in terms of proportion of delays** and it can be
explained due to the fact that its the holiday season! Apart from this,
**the evening, night time is particularly prone to delays for all
months**! Weekdays and weekends show the same pattern, with more delays
on the weekend.
