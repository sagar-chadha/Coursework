## Basic Probability Concepts 

### Question 1

"Visitors to your website are asked to answer a single survey question before they get access to the content on the page. Among all of the users, there are two categories: Random Clicker (RC), and Truthful Clicker (TC). There are two possible answers to the survey: yes and no. Random clickers would click either one with equal probability. You are also giving the information that the expected fraction of random clickers is 0.3. After a trial period, you get the following survey results: 65% said Yes and 35% said No.

What fraction of people who are truthful clickers answered yes?"

### Solution -

Given information - <br> 1) Probability of Yes (Y) given Random clicker
(RC), P(Y\| RC) = 0.5 <br> 2) Probability of No (N) given Random clicker
(RC), P(N\| RC) = 0.5 <br> 3) Probability of a random clicker (RC),
P(RC) = 0.3 <br> 4) Since there are just two possible outcomes,
Probability of a true clicker (TC) is - 1 - P(RC) = 0.7

65% people in the survey said yes, 35% said no, which is to say that if
100 people were to participate in the survey - <br> (assuming ‘p’
proportion of TCs say yes)

| Participants   | 100      | Yes       | No              |
|----------------|----------|-----------|-----------------|
| True Callers   | 70 (0.7) | 70 \* *p* | 70 \* (1 − *p*) |
| Random Callers | 30 (0.3) | 30 \* 0.5 | 30 \* 0.5       |

Which means that out of the random callers, 15 would say ‘Yes’, 15 would
say ‘No’. We need to evaluate how many of the True Clickers said ‘Yes’.

Basically the total number of people who said yes is - 15 + 70 \* *p*
out of a total of 100 people. <br>

Knowing this fraction to be 65 or 0.65, we can calculate - <br>

(15 + 70 \* *p*)/100 = 0.65. This gives us - <br>

*p* = 5/7. <br>

**So 5/7th of the true clickers said yes**

------------------------------------------------------------------------



### Question 2

"Imagine a medical test for a disease with the following two attributes:

The sensitivity is about 0.993. That is, if someone has the disease, there is a probability of 0.993 that they will test positive.
The specificity is about 0.9999. This means that if someone doesn't have the disease, there is probability of 0.9999 that they will test negative.
In the general population, incidence of the disease is reasonably rare: about 0.0025% of all people have it (or 0.000025 as a decimal probability).

Suppose someone tests positive. What is the probability that they have the disease? In light of this calculation, do you envision any problems in implementing a universal testing policy for the disease?"

### Solution - 

Let’s draw the following confusion matrix for the test described in the
problem - <br>

Let’s say a 1,000,000,000 (1 billion) people take the test.

| Test Result | Disease  | No disease  |
|-------------|----------|-------------|
| Positive    | 24,825   | 100,000     |
| Negative    | 175      | 999,875,000 |
| Total       | 25,000   | 999,975,000 |
| Probability | 0.000025 | 0.999975    |

Probability that they have a disease given that test is positive - <br>
P(Disease\|Positive Test) <br> =(24,825)/(24,825 + 100,000) <br> =19.89%
<br> So, there is only a ~20% chance of someone having a disease when
the test result is positive. This is not very conclusive of having the
disease, therefore most people who test positive would have to take the
test multiple times before being sure. If people who tested positive, go
back again for the test, the prior probabilities have now changed to -

P(Disease) = 0.1989 <br> P(No Disease) = 0.8011

| Test Result | Disease   | No disease |
|-------------|-----------|------------|
| Positive    | 24,653.90 | 10.00      |
| Negative    | 173.79    | 99,987.31  |
| Total       | 24,827.69 | 99,997.31  |
| Probability | 0.1989    | 0.8011     |

This time, people who tested positive can be 99.96% sure of having the
disease.

**So, the only hindrance to the implementation is whether or not this
test is too expensive. If not, I don’t see a reason for it not to be
implemented.**
