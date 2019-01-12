## Job Salary Prediction
![image](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/salary_prediction.jpg)

### The Task
This was a [challenge](https://www.kaggle.com/c/job-salary-prediction#description) put forth by Adzuna - a new search engine for job, property, and car ads based in the UK - on Kaggle. Adzuna wants to build a prediction engine for the salary of any UK job ad, so they can make improvements in the experience of users searching for jobs, and help employers and jobseekers figure out the market worth of different positions.

The challenge here is to effectively use categorical variables and job descriptions (text data) to predict the salary of a particular job. Having worked with numerical data in the past, the biggest challenge in this task is to effectively manipulate and transform `Job Descriptions` so that they could be effectively used for prediction.

### Nature of ML problem
Although originally a regression type problem, I have simplified this problem to predict only whether a job would fall under the *high salary* or *low salary* category, thus converting this to a **classification problem**. All salaries above the 75th percentile mark are high salaries and the rest are classified as low salaries. This is an **imbalanced class problem** since high salaries appear only 25% of the time.

 ### Approach
 I tried prediction job salary categories in three different ways - <br>
 * Use all categorical variables other than Job Description for the prediction.
 * Use only job description for the prediction.
 * Use a combination of job description and other variables for the challenge.
 
 ### Concepts Used
 * Text Mining concepts such as stopwords, tokenization, stemming, lemmatization, Zipf's law (for a brief discussion of these concepts you can refer [my article](https://medium.com/@sagarchadha007/words-are-more-than-wind-db67fd8a7746) on Medium).
 * Python's `nltk` library to manipulate text data.
 * Bernoulli and Multinomial Naive Bayes algorithms.
 * Mutual Information
 
 ### Models Tried
 * Multinomial Naive Bayes
 * Bernoulli Naive Bayes
 
### Results Obtained
Given that this is a problem with imbalanced classes, accuracy is not the right metric here. However, given that the baseline accuracy is 75% (a model that says low salary would be correct 75% of the time) I still need a model with an accuracy greater than 75%. 

**A multinomial Naive Bayes model gave me the best accuracy of 82.4%. The AUROC metric was at 0.76.**

### Repository Structure
`job_salary_prediction.ipynb` is the jupyter notebook with my detailed approach and analysis.
