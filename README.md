# outage_project: data driven approach to understand severe power outage characteristics
this is a project for DSC 80 at UCSD 
by Antony Munkhchuluun

---
## Introduction
### Question: What are the characteristics of severe power outages?
### Significance: My analysis delves a bit into what variables and characteristics energy companies should look out for when predicting the location and the severity of the next major power outage.
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
I took the necessary steps to convert the value of certain columns so that I can do my intended analysis on it. More specifically, it made it easier for me to run transform methods using various types of transformer modules. In partucular, I converted the columns OUTAGE.DURATION, CUSTOMERS.AFFECTED so that I can use it for my baseline and final models. I have also took out 43 cols from the raw dataframe and used only 10 of the columns for my cleaning and analysis processes. Below, I have splitted up the cleaned DataFrame in half so that it displays nicer!

| U.S._STATE   | OUTAGE.START.DATE.TIME   | OUTAGE.RESTORATION.DATE.TIME   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |
|:-------------|:-------------------------|:-------------------------------|:-------------------|:------------------------|
| Minnesota    | 2011-07-01 17:00:00      | 2011-07-03 20:00:00            | severe weather     | nan                     |
| Minnesota    | 2014-05-11 18:38:00      | 2014-05-11 18:39:00            | intentional attack | vandalism               |
| Minnesota    | 2010-10-26 20:00:00      | 2010-10-28 22:00:00            | severe weather     | heavy wind              |
| Minnesota    | 2012-06-19 04:30:00      | 2012-06-20 23:00:00            | severe weather     | thunderstorm            |
| Minnesota    | 2015-07-18 02:00:00      | 2015-07-19 07:00:00            | severe weather     | nan                     |

|   HURRICANE.NAMES |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   RES.SALES |   RES.CUSTOMERS |
|------------------:|------------------:|---------------------:|------------:|----------------:|
|               nan |              3060 |                70000 | 2.33292e+06 |     2.30874e+06 |
|               nan |                 1 |                  nan | 1.58699e+06 |     2.34586e+06 |
|               nan |              3000 |                70000 | 1.46729e+06 |     2.30029e+06 |
|               nan |              2550 |                68200 | 1.85152e+06 |     2.31734e+06 |
|               nan |              1740 |               250000 | 2.02888e+06 |     2.37467e+06 |

### Distribution of the number of power outages amongst the U.S states

<iframe
  src="state-outage-bar.html"
  width="600"
  height="800"
  frameborder="0"
></iframe>

#### As you may be able to see that California has the highest number of outages in our dataset, whereas Alaska has the least. 

### Number of power outages relative to residential sector electricity consumption by state

<iframe
  src="step2-outage-scatter.html"
  width="600"
  height="800"
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
  height="800"
  frameborder="0"
></iframe>

#### As you can see from the red dashed line, the difference between the distributions of all CAUSE.CATEGORY.DETAIL and CAUSE.CATEGORY.DETAIL where HURRICANE.NAMES are missing is much more than our 1000 simulated differences. Thereby, it concludes that the missingness of HURRICANE.NAMES column is dependant on CAUSE.CATEGORY.DETAIL column.


