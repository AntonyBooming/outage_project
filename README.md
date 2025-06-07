A Data-Driven Approach to Understand Severe Power Outage Characteristics
this is a project for DSC 80 at UCSD 

by Antony Munkhchuluun

---
## Introduction
### Question: What are the characteristics of severe power outages?
### Significance: This analysis provides insight into the key factors associated with severe power outages. By identifying patterns in outage causes, customer impact, and regional differences, the findings can help both residents and energy providers better anticipate where and how future major outages may occur—potentially improving preparedness and response strategies.
Dataframe: 1535 rows and 10 columns


| Columns                       | Description                                                                                          |
|------------------------------|------------------------------------------------------------------------------------------------------|
| **U.S._STATE**               | Represents all the states in the continental U.S.                                                    |
| **OUTAGE.START.DATE.TIME**   | The day and time when the outage event started (as reported by the corresponding Utility).           |
| **OUTAGE.RESTORATION.DATE.TIME** | The day and time when power was restored to all customers (as reported by the corresponding Utility). |
| **CAUSE.CATEGORY**           | Categories of the events causing the major power outages.                                            |
| **CAUSE.CATEGORY.DETAIL**    | Detailed description of the event categories causing the major power outages.                        |
| **HURRICANE.NAMES**          | If the outage is due to a hurricane, the hurricane name is given here.                               |
| **OUTAGE.DURATION**          | Duration of outage events (in minutes).                                                              |
| **CUSTOMERS.AFFECTED**       | Number of customers affected by the power outage event.                                              |
| **RES.SALES**                | Electricity consumption in the residential sector (megawatt-hour).                                   |
| **RES.CUSTOMERS**            | Annual number of customers served in the residential electricity sector of the U.S. state.           |

---
## Data Cleaning and Exploratory Data Analysis
I prepared the dataset by transforming key columns to enable more effective analysis. Specifically, I converted OUTAGE.DURATION and CUSTOMERS.AFFECTED to formats compatible with various transformer modules, making it easier to apply preprocessing techniques for my baseline and final models. I also reduced the original dataset from 43 columns to a focused set of 10 columns for cleaning and analysis. Below, I’ve split the cleaned DataFrame in three for a more readable display.

| U.S._STATE   | OUTAGE.START.DATE.TIME   | OUTAGE.RESTORATION.DATE.TIME   |
|:-------------|:-------------------------|:-------------------------------|
| Minnesota    | 2011-07-01 17:00:00      | 2011-07-03 20:00:00            |
| Minnesota    | 2014-05-11 18:38:00      | 2014-05-11 18:39:00            |
| Minnesota    | 2010-10-26 20:00:00      | 2010-10-28 22:00:00            |
| Minnesota    | 2012-06-19 04:30:00      | 2012-06-20 23:00:00            |
| Minnesota    | 2015-07-18 02:00:00      | 2015-07-19 07:00:00            |

|   HURRICANE.NAMES | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |
|------------------:|:-------------------|:------------------------|
|               nan | severe weather     | nan                     |
|               nan | intentional attack | vandalism               |
|               nan | severe weather     | heavy wind              |
|               nan | severe weather     | thunderstorm            |
|               nan | severe weather     | nan                     |

|   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   RES.SALES |   RES.CUSTOMERS |
|------------------:|---------------------:|------------:|----------------:|
|              3060 |                70000 | 2.33292e+06 |     2.30874e+06 |
|                 1 |                  nan | 1.58699e+06 |     2.34586e+06 |
|              3000 |                70000 | 1.46729e+06 |     2.30029e+06 |
|              2550 |                68200 | 1.85152e+06 |     2.31734e+06 |
|              1740 |               250000 | 2.02888e+06 |     2.37467e+06 |

### Distribution of the number of power outages amongst the U.S states

<iframe
  src="state-outage-bar.html"
  width="600"
  height="800"
  frameborder="0"
></iframe>

#### As you may be able to see that California has the highest number of outages in our dataset, whereas Alaska has the least. 

### Number of power outages relative to residential sector electricity consumption by state(you can touch on the points to see specifics!)

<iframe
  src="step2-outage-scatter.html"
  width="600"
  height="650"
  frameborder="0"
></iframe>

#### From this bivariate visualization, you are able to see that as there's more units of electricity consumption in the residential sector, the number of outages increases. This is visible throughout each state where each states have different numbers of outages. The first few rows of this tabular distribution looks like this:

| U.S._STATE   |   RES.SALES |
|:-------------|------------:|
| Alabama      | 1.44123e+07 |
| Alaska       | 0           |
| Arizona      | 7.1963e+07  |
| Arkansas     | 3.95144e+07 |
| California   | 1.54653e+09 |

#### Interestingly enough, there's no residential sector electricity consumption recorded in our DataFrame.

---

## Assessment of Missingness
I believe the column HURRICANE.NAMES is NMAR and as I looked for additional information on it, it looked like the column CAUSE.CATEGORY.DETAIL had an influence on the column HURRICANE.NAMES, thereby making it MAR. To prove this:

<iframe
  src="step3-outage-MAR.html"
  width="600"
  height="450"
  frameborder="0"
></iframe>

#### As you can see from the red dashed line, the difference between the distributions of all CAUSE.CATEGORY.DETAIL and CAUSE.CATEGORY.DETAIL where HURRICANE.NAMES are missing is much more than our 1000 simulated differences. Thereby, it is likely plausible that the missingness of HURRICANE.NAMES column is dependant on CAUSE.CATEGORY.DETAIL column.

Conversely, let's now see what column HURRICANE.NAMES column doesn't depend on.

<iframe
  src="step3-outage-MCAR.html"
  width="600"
  height="650"
  frameborder="0"
></iframe>

#### By the position of our observed test statistic, you can see that it is a pretty common occurance within our simulated test statistics. Therefore, it is likely possible that the missingess of HURRICANE.NAMES column doesn't depend on RES.CUSTOMERS column.

---

## Hypothesis Testing 
Null Hypothesis: On average, the outage duration caused by severe weather is the same as the outage duration caused by public appeal.
Alternative Hypothesis: On average, the outage duration caused by severe weather is greater than the outage duration caused by public appeal.
#### Test Statistic: Difference in group means
#### Significance level: 0.05
#### P-Value: 0.0

<iframe
  src="step4-outage-hypotest.html"
  width="600"
  height="650"
  frameborder="0"
></iframe>

### We reject the null hypothesis! This means that our observed difference(red dashed line) between the average outage duration caused by severe weather and average outage duration caused by public appeal is not common at all under the null hypothesis. Therefore, we can conclude that there's a pattern where if the power outage is caused by severe weather, you are likely to expect a longer outage than if it was caused by public appeal on average.

---

## Framing a prediction problem
Prediction problem: What is the outage duration gonna be like based on outage cause category and the number of customers affected by the outage.
Type: Regression Line Model
Response Variable: Outage Duration(very important to know if the power outage is gonna be severe or not based on different characteristics)
Known Info: Outage causes, # of customers affected

---

## Baseline Model
### Description of my baseline model:
My linear regression model is trained on outage cause categories and the number of customers affected by the certain outage. I left the quantative feature(# of customers affected) to passthrough the pipeline without any encodings. But, I passed the categorical feature(outage cause category) through OneHotEncoder() so that it is more effective for my model.

### Result of my baseline model:
The performance of this baseline model had a R^2 score of 0.069944362461548 on unseen data.

My intuition told me that this wasn't a good model because of the magnitude of values in both features. The values of number of customers affected feature are relatively large compared to the OneHotEncoder() transformed outage cause category feature which only consists of values close to 0. Therefore, the number of customers affected feature dominates the other feature when it comes to predicting the response variable. In other words, the 0s and 1s from one-hot encoding will have very little effect in comparison.

---

## Final Model
Features added:
1. Standardized # of customer affected
Why:
This is good for the prediction task because it brings down the feature around the same scale as the one hot encoded 0s and 1s of the other feature.
2. Polynomials
Why:
This makes my predictive model better because the regression line will be shaped more like where the data points are, meaning it hits more data as the predictive line progresses, which should result in better accuracy.

Result:
Standardizing the # of customer affected brought the score up to 0.1908772716231314
Polynomial(degree=14) made the accuracy better to 0.25537566050097293

The method I used to come up with the best performing hyperparameter was by a manual iteration where I performed different polynomial degrees and degree 14 ended up being the best transformation.

Below(provided different model performances of polynomials of standardized # of customers affected):

<iframe
  src="step7-outage-polyn.html"
  width="600"
  height="650"
  frameborder="0"
></iframe>

---

## Fairness Analysis
Group large amount of customers: RES.CUSTOMERS > middle value(4216573.0)
Group small amount of customers: RES.CUSTOMERS < middle value(4216573.0)
Evaluation metric: R^2
Null Hypothesis: Our model is fair. Its R^2 score for large amount of customers and small amount of customers are roughly the same, and any differences are due to random chance.
Alternative Hypothesis: Our model is unfair. Its R^2 score for large amount of customers is better than small amount of customers.
Test Statistic: Difference in R^2 between large amount of customers group and small amount of customers group
Significance Level: 0.05
P-value: 0.176
Conclusion: We fail to reject the null hypothesis, meaning that our model is fair among large amount of customers and small amount of customers.

<iframe
  src="step8-outage-fairnessanalysis.html"
  width="600"
  height="650"
  frameborder="0"
></iframe>

From this histogram, you can see that our observed difference is as common as our simulated differences, being in the 82th percentile among them.




