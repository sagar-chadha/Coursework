## H1B Visa Status Prediction
![](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/Predictive%20Modelling/H1B%20Visa%20Analysis/H1bvisa.jpeg)
### The Task
Using data of over 500,000 visa applications to the US between 2011 and 2017, we try to predict the 'Status' of the visa application based on predictors such as - <br>
* Wage offered
* employer
* employer region

The dataset can be found [here](https://www.kaggle.com/trivedicharmi/h1b-disclosure-dataset).

### Nature of ML problem
Since we are predicting whether or not a visa application was Certified, this is a **classification problem**. Not many visa applications are denied if we look at the overall proportion of denials. That makes this a **imbalanced class problem** with the 'Denied' category being the rare class.

### Concepts Used
This project was undertaken to get some hands on practice with the various machine learning algorithms out there. We did some data cleaning as well, but thats not important here and isn't stressed on in the final analysis. We used - 
* Logistic Regression
* Stepwise Logistic Regression
* Threshold selection for models.
* Linear Discriminant Analysis
* Random Forest
* XGBoost

### Repository Structure
[`H1B_Visa_Status.rmd`](https://github.com/sagar-chadha/Coursework/blob/master/Predictive%20Modelling/H1B_Visa_Status_Prediction/H1B_Analysis.Rmd) has the R notebook with the code used for making the report. <br>
[`H1B_Visa_Status.md`](https://github.com/sagar-chadha/Coursework/blob/master/Predictive%20Modelling/H1B_Visa_Status_Prediction/H1B_Analysis.md) is the final markdown report with the analysis.
