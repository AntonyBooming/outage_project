# outage_project: data driven approach to understand severe power outage characteristics
this is a project for DSC 80 at UCSD 

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
<iframe
  src="state-outage-bar.html"
  width="1000"
  height="800"
  frameborder="0"
></iframe>