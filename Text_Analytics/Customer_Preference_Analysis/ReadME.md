## Customer Preference Analysis
![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Text_Analytics/Customer_Preference_Analysis/customer%20preference.jpg)

### The Task
The goal is to scrape the entry level luxury sedan forum from Edmunds and analyse user comments. I use cars here since they are a 'high involvement' product, meaning that people will usually research a lot and care to give reviews about their purchases on social media platforms.

For entry level luxury car brands, we will look at which brands are people most talking about, what are they mentioning most when it comes to product features, which two brands are being compared heavily, etc. Based off of this analysis, we need to then provide insights and recommendations to brand, product and advertising managers.

### Approach
My approach for this task is as follows - 
* Use Python's `selenium` package to scrape ~6000 user reviews from Edmunds.
* Look for mention of model names in the user reviews and replace them with Brand names since we are only looking for Brand level insights.
* Clean user comments - remove stop words, punctuations, numbers, etc
* Make a list of automobile features such as safety, performance, etc. Look for words in the comments that map to these features and replace those words with feature names.
* Get association between brands (MDS Plot) and between brands and features using lift values.
* Analyze which is the most aspirational brand - I consider this to be the brand that's most talked about in a positive manner.

### Concepts Used
* **Lift** - Lift quantifies the question - 'Do these terms occur together very frequently as compared to their overall occurence'.
* **MDS Plot** - The Multi-Dimensional scaling plot, or MDS plot, is sort of like a scatterplot that quantifies the similarity or dissimilarity between products. The farther two points are, the less their association is.

### Recommendations

#### Based on brand associations
Our MDS plot confirms the suspicions that **Cadillac has successfully brought itself back from the dead**. Enthusiasts have begun saying it has surpassed the German luxury brand BMW. To quote a recent verge article comparing the ATS-V, Cadillac’s prime performance model, with the BMW M3, “The idea that a Cadillac could ever compete with a M3 would once have been heresy. But the current generation M3 has lost its way.” This trend can be seen on the plot, where **Cadillac has successfully rejoined the cluster ranks of true luxury with strong associations with Lexus, Infiniti and Mercedes.**

#### Based on association between brands and features
* Honda's advertising manager is working with a product with several marketable strengths, such as efficiency, safety, and comfort. If the manager could do more to get people to associate performance with Honda, then Honda would have the strongest reputation that it possibly could among Edmunds forum users.
* BMW is far from being the "ultimate driving machine" on Edmunds. BMW and the different car attributes have comparitively low lift values to the other top brands. This implies that when people are talking about different desirable car features, BMW does not come to mind.

### Repository Structure
[`Customer_Preference_Analysis.ipynb`](https://github.com/sagar-chadha/Coursework/blob/master/Text_Analytics/Customer_Preference_Analysis/Customer_Preference_Analysis.ipynb) is the jupyter notebook with my analysis. <br>
I have also added [`Practice_Selenium.ipynb`](https://github.com/sagar-chadha/Coursework/blob/master/Text_Analytics/Customer_Preference_Analysis/Practice_Selenium.ipynb) as a short guide on how to use the `selenium` library.
