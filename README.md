# What Makes a Power Outage Severe? A Data-Driven Investigation

**By Antony Munkhchuluun** · UC San Diego, DSC 80

---

Power outages aren't random — some knock out power for millions of people for days, while others are resolved in minutes. What separates a minor flicker from a catastrophic grid failure? I wanted to find out.

Using a dataset of **1,535 major U.S. power outages**, I investigated the causes, patterns, and predictors of severe outages — combining exploratory analysis, statistical testing, and predictive modeling to uncover what really drives outage severity.

---

## The Data

The dataset covers major power outages across the continental U.S., with 10 key features:

| Column | Description |
|--------|-------------|
| `U.S._STATE` | State where the outage occurred |
| `OUTAGE.START.DATE.TIME` | When the outage began |
| `OUTAGE.RESTORATION.DATE.TIME` | When power was fully restored |
| `CAUSE.CATEGORY` | High-level cause (e.g., severe weather, intentional attack) |
| `CAUSE.CATEGORY.DETAIL` | Detailed cause description |
| `HURRICANE.NAMES` | Hurricane name, if applicable |
| `OUTAGE.DURATION` | How long the outage lasted (minutes) |
| `CUSTOMERS.AFFECTED` | Number of customers who lost power |
| `RES.SALES` | Residential electricity consumption (MWh) |
| `RES.CUSTOMERS` | Annual residential customers served in that state |

---

## Data Cleaning & Exploratory Analysis

I reduced the raw dataset from 43 columns down to the 10 most relevant features, and converted `OUTAGE.DURATION` and `CUSTOMERS.AFFECTED` into numeric types for modeling compatibility.

**Where do outages happen most?**

California leads the dataset with 210 recorded outages — far more than any other state. This makes sense given its massive population, aging grid infrastructure, and wildfire-prone climate.

<iframe src="state-outage-bar.html" width="800" height="800" frameborder="0"></iframe>

**Does electricity consumption predict outage frequency?**

I wanted to know: do states that consume more power also experience more outages? The scatterplot below reveals a clear positive trend — higher residential electricity consumption correlates with more outages. California is a striking outlier, with over 1.5 billion MWh in residential sales and 210 outages.

<iframe src="step2-outage-scatter.html" width="800" height="800" frameborder="0"></iframe>

| State | Residential Sales (MWh) | # of Outages |
|-------|------------------------|--------------|
| Alabama | 14,412,302 | 6 |
| Alaska | 0 | 1 |
| Arizona | 71,963,016 | 28 |
| Arkansas | 39,514,393 | 25 |
| California | 1,546,533,874 | 210 |

---

## Missingness Analysis — Is Hurricane Data Really Missing at Random?

At first glance, the `HURRICANE.NAMES` column looks like it might be **NMAR** (Not Missing At Random) — perhaps utilities simply forgot to record hurricane names. But I suspected something more systematic was going on.

**Hypothesis:** The missingness of `HURRICANE.NAMES` depends on `CAUSE.CATEGORY.DETAIL` — because hurricane names would only be recorded when the cause was actually a hurricane.

I ran a permutation test to check:

<iframe src="step3-outage-MAR.html" width="800" height="450" frameborder="0"></iframe>

The observed difference in `CAUSE.CATEGORY.DETAIL` distributions (red dashed line) sits far beyond all 1,000 simulated differences — confirming that `HURRICANE.NAMES` is **MAR**, dependent on the cause category. It's not random omission; the data is simply absent when it's not applicable.

**What about residential customers?** I also tested whether missingness depended on `RES.CUSTOMERS` — and found no relationship:

<iframe src="step3-outage-MCAR.html" width="600" height="650" frameborder="0"></iframe>

The observed statistic falls squarely within the simulated distribution, confirming `HURRICANE.NAMES` missingness is **independent of residential customer count**.

---

## Hypothesis Test — Do Severe Weather Outages Last Longer?

**Question:** Is there a statistically significant difference in outage duration between severe weather events and public appeal outages?

- **Null hypothesis:** Average outage duration is the same for both causes
- **Alternative hypothesis:** Severe weather outages last longer on average
- **Test statistic:** Difference in group means
- **Significance level:** α = 0.05

<iframe src="step4-outage-hypotest.html" width="600" height="650" frameborder="0"></iframe>

**Result: p-value = 0.0 → We reject the null hypothesis.**

The observed difference is so extreme it never appeared in 1,000 permutations. This is strong evidence that severe weather outages are systematically longer — likely because weather damage requires physical repair crews rather than just a switch flip.

---

## Predicting Outage Duration

**Can we predict how long an outage will last before it's over?**

- **Prediction problem:** Regression — predict `OUTAGE.DURATION`
- **Features used at prediction time:** `CAUSE.CATEGORY` and `CUSTOMERS.AFFECTED`
- **Why these features?** Both are known early in an outage event, making this model useful for real-time response planning

### Baseline Model

My first model passed `CUSTOMERS.AFFECTED` through unchanged and one-hot encoded `CAUSE.CATEGORY`.

**Baseline R² = 0.0699**

Poor performance — but illuminating. The large raw values of `CUSTOMERS.AFFECTED` (ranging into the hundreds of thousands) were drowning out the one-hot encoded cause features (which are just 0s and 1s). The model was essentially ignoring cause category entirely.

### Final Model — Feature Engineering Fixes Everything

I made two targeted improvements:

1. **Standardized `CUSTOMERS.AFFECTED`** — scaled to the same range as the encoded features so both contribute equally → **R² jumped to 0.1908**
2. **Polynomial features (degree=14)** — allowed the model to capture nonlinear relationships between customer scale and outage duration → **R² improved to 0.2553**

I selected degree=14 through manual iteration across polynomial degrees:

<iframe src="step7-outage-polyn.html" width="900" height="650" frameborder="0"></iframe>

A 3.6x improvement in R² from baseline to final model — driven entirely by thoughtful feature engineering, not a more complex algorithm.

---

## Fairness Analysis — Does the Model Work Equally for All States?

A model that performs well on average can still be systematically unfair to certain groups. I tested whether my model performs equally for states with large vs. small residential customer bases.

- **Group A:** States where `RES.CUSTOMERS` > median (4,216,573)
- **Group B:** States where `RES.CUSTOMERS` < median
- **Metric:** R²
- **Null hypothesis:** Any R² difference between groups is due to random chance
- **Significance level:** α = 0.05

<iframe src="step8-outage-fairnessanalysis.html" width="900" height="650" frameborder="0"></iframe>

**Result: p-value = 0.176 → Fail to reject the null hypothesis.**

The observed R² difference falls at the 82nd percentile of simulated differences — well within the range of chance. The model performs equitably across both large and small states, which is an important property for a model intended to support real-world outage response planning.

---

## Key Takeaways

- **Cause matters more than location** — severe weather is the single strongest driver of long outages
- **Scale correlates with frequency** — high-consumption states experience more outages, but this relationship is imperfect
- **Feature engineering > model complexity** — a simple linear regression improved 3.6x with just standardization and polynomial features
- **The model is fair** — no evidence of differential performance across large vs. small states

---

*Full analysis conducted in Python using Pandas, scikit-learn, and Plotly. UC San Diego, DSC 80.*
