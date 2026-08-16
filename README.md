<p align="center">
  <img src="docs/images/00-citibike-banner.jpg" alt="NYC Citi Bike" width="640">
</p>

# NYC Citi Bike — Demand & Rider Behaviour Analysis

> **Exploratory analysis of 18,449 bike-share trips to understand demand concentration, rider behaviour, and how outliers can distort business conclusions.**

[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-orange)](https://matplotlib.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter)](https://jupyter.org/)

---

## The question

**What does the data tell us about how people actually use a bike-share system — and which conclusions can we trust?**

I analysed NYC Citi Bike trip data from the Jersey City service area to identify:

* Where demand is concentrated
* When the system is most heavily used
* Who the riders are
* Whether age is related to trip duration
* How subscriber and casual usage differs
* How outliers can create misleading business conclusions

The analysis covers **20,400 raw records**, reduced to **18,449 cleaned trips** across **50 stations and 500 bikes**, spanning January to December 2017.

> **Basis for every figure below:** all statistics are computed on the full 18,449-row cleaned dataset, with no records excluded. This matters — the notebook drops the single longest trip partway through, which shifts every downstream count. Where that distinction changes a number, it is called out explicitly.

---

## Executive Summary

### 1. The system is primarily commuter infrastructure

**98.1% of trips come from subscribers.**

Average weekday demand is approximately **75% higher than weekend demand** (3,003 vs 1,716 trips per day), with Wednesday the busiest day.

This suggests the system is used predominantly as **everyday transportation rather than weekend leisure**.

### 2. Demand is highly concentrated

**Grove St PATH alone accounts for 12.6% of all trips.**

The three busiest stations — Grove St PATH, Exchange Place and Hamilton Park — account for approximately **26% of total demand**.

These locations are closely connected to major transit routes, highlighting the importance of **first/last-mile transportation and station rebalancing**.

### 3. Age does not meaningfully explain trip duration

The correlation between age and trip duration is:

**r = -0.002**

In practical terms, there is **no meaningful linear relationship** between the two variables.

Trip duration appears to be driven much more by the journey itself than by the rider's age.

### 4. One outlier completely changes the story

The 75+ age group has an average trip duration of **47.9 minutes**.

That sounds significant — until you look at the distribution.

The group's median trip duration is only **4 minutes**, based on 57 trips.

One **2,422-minute trip** is responsible for most of the difference. That single ride accounts for **88.8% of every minute the group logged**. Remove it and the average falls to **5.5 minutes**.

Without checking the distribution, this could easily become a misleading statement such as:

> "Older riders take substantially longer trips."

The data does not support that conclusion.

### 5. The biggest lesson is methodological

**Averages can be technically correct and still tell the wrong story.**

For skewed operational data, especially when sample sizes are small, the median and distribution should be examined before turning an aggregate statistic into a business conclusion.

---

## Key Insights

### 🚲 Demand is concentrated around transit

| Station        | Trips |
| -------------- | ----: |
| Grove St PATH  | 2,319 |
| Exchange Place | 1,342 |
| Hamilton Park  | 1,185 |
| Sip Ave        | 1,184 |
| Morris Canal   |   768 |

The top stations are not randomly distributed. Grove St PATH and Exchange Place — the two busiest — are both PATH train stops, as is Newport PATH at sixth. This suggests Citi Bike is functioning heavily as a **last-mile mobility network** feeding rail.

Note how close Hamilton Park and Sip Ave are — 1,185 against 1,184. A single trip separates third place from fourth, so any ranking below the top two should be treated as a tie rather than an order.

**Business implication:** rebalancing resources should be concentrated around high-volume transit hubs rather than distributed evenly across the network, and timed to train schedules rather than to bike demand alone.

![Station demand](docs/images/01-hero-top-pickup-locations.png)

---

### 📊 Trip duration is heavily right-skewed

| Metric  |  Duration |
| ------- | --------: |
| Mean    |   9.6 min |
| Median  |     5 min |
| Minimum |     1 min |
| Maximum | 6,515 min |

The mean is almost twice the median.

The maximum trip lasted more than **108 hours**, which is highly unlikely to represent a normal bike journey and is more plausibly a data-quality or unreturned-bike record. 103 trips exceed one hour.

This is why the analysis does not treat the mean as sufficient evidence on its own.

![Trip duration distribution](docs/images/07-trip-duration-distribution.png)

---

### 🔎 The 75+ finding disappears when the distribution is examined

| Age group |         Mean |    Median | Trips |
| --------- | -----------: | --------: | ----: |
| 18–24     |     11.9 min |    10 min |    56 |
| 25–34     |      9.0 min |     6 min | 4,434 |
| 35–44     |     10.3 min |     5 min | 8,377 |
| 45–54     |      8.1 min |     5 min | 3,238 |
| 55–64     |     10.2 min |     6 min | 1,600 |
| 65–74     |      7.4 min |     6 min |   687 |
| 75+       | **47.9 min** | **4 min** |    57 |

The 75+ group's average is driven by a single **2,422-minute observation**. 54 of the group's 57 trips lasted under 10 minutes.

Read by mean, the 75+ group looks like the longest riders in the system. Read by median, they are the **shortest**.

![Trip duration by age group, mean versus median](docs/images/06-mean-vs-median-by-age-group.png)

This is a useful example of why:

**mean → investigate → visualize → validate → conclude**

rather than:

**mean → story**

---

### 👥 The rider base is concentrated in ages 25–54

The **25–54 age range accounts for 87% of all trips**.

The 35–44 group alone represents 8,377 trips, making it by far the largest rider segment in the dataset.

At the same time, riders under 25 account for only **56 trips** — 0.3% of the dataset — making them almost absent from this sample.

This creates an interesting business question around pricing, awareness and adoption — but the dataset alone does not establish the reason.

![Rentals by age group](docs/images/03-rentals-by-age-group.png)

---

### 📅 Weekday and weekend usage behave differently

| Day       | Subscriber | One-time |     Total |
| --------- | ---------: | -------: | --------: |
| Monday    |      2,715 |       57 |     2,772 |
| Tuesday   |      2,647 |       30 |     2,677 |
| Wednesday |      3,590 |       43 | **3,633** |
| Thursday  |      3,239 |       20 |     3,259 |
| Friday    |      2,645 |       30 |     2,675 |
| Saturday  |      1,612 |  **102** |     1,714 |
| Sunday    |      1,651 |       68 |     1,719 |

The pattern suggests two distinct usage behaviours:

**Weekdays → subscription / commuting**

**Weekends → lower overall demand + relatively more casual users**

Saturday is particularly interesting: it has the **highest number of one-time users**, despite having the lowest total ride volume. Casual riders make up 6.0% of Saturday trips against 0.6% on Thursday — a tenfold difference in composition.

![Usage by weekday and user type](docs/images/04-rentals-by-usertype-weekday.png)

---

### 📉 Age and trip duration are unrelated

![Age versus trip duration](docs/images/05-age-vs-trip-duration.png)

The scatter shows the same thing the correlation coefficient does: a dense band of short trips at every age, with scattered extreme values following no age pattern.

---

## Business Recommendations

| Priority  | Recommendation                                             | Why                                           |
| --------- | ---------------------------------------------------------- | --------------------------------------------- |
| 🔴 High   | Prioritize rebalancing around the busiest transit stations | Top 3 stations generate ~26% of trips         |
| 🔴 High   | Align operational capacity with weekday demand             | Weekday demand is ~75% higher than weekends   |
| 🟠 Medium | Focus casual-user acquisition on weekends                  | Casual usage is relatively concentrated there |
| 🟠 Medium | Use median duration alongside mean                         | Trip duration contains extreme outliers       |
| 🟢 Low    | Investigate the under-25 adoption gap                      | Only 56 trips come from riders under 25       |

### What I would *not* recommend

I would **not** recommend age-specific product or marketing decisions based on trip duration.

The correlation between age and duration is essentially zero, and the most visually dramatic age-group result is explained by a single outlier.

**The data isn't strong enough to support that conclusion.**

---

## Analytical Approach

The analysis follows a simple analytical workflow:

```text
Raw Data
   ↓
Data Cleaning
   ↓
Exploratory Analysis
   ↓
Distribution & Outlier Analysis
   ↓
Segmentation
   ↓
Visual Validation
   ↓
Business Recommendations
```

### Data preparation

* Removed duplicate records
* Removed rows containing missing values
* Converted trip duration from text to numeric (stripping thousands separators)
* Parsed timestamps into datetime fields
* Used temporal variables such as weekday and month
* Reduced the dataset from **20,400 → 18,449 records** (9.6% removed)

### Analysis

The analysis uses:

* Descriptive statistics
* Distribution analysis
* Grouped aggregations
* Cross-tabulation
* Correlation analysis
* Outlier investigation
* Station demand ranking
* Age segmentation
* Subscriber vs. casual-user analysis
* Data visualization

---

## Tech Stack

**Python**
**Pandas** — data cleaning, transformation and analysis
**Matplotlib** — visualization
**Jupyter Notebook** — analysis and documentation

---

## Repository Structure

```text
nyc-bikeshare-demand-analysis/
│
├── notebook.ipynb                                  # Full analysis
├── New York Citi Bikes_Raw Data - NYCitiBikes.csv  # Raw data (20,400 rows)
├── bikes_dataset.csv                               # Cleaned data
│
└── docs/
    └── images/                                     # Charts used in this case study
```

The notebook contains the complete analysis and reproduces the statistics and visualizations presented in this case study.

---

## Run the Analysis

```bash
git clone https://github.com/niksaderek/nyc-bikeshare-demand-analysis.git

cd nyc-bikeshare-demand-analysis

pip install pandas matplotlib jupyter

jupyter notebook notebook.ipynb
```

---

## Data Source

The analysis uses publicly available Citi Bike trip data covering the Jersey City service area in 2017, containing ride timestamps, start and end stations, bike identifiers, user type, birth year and derived temporal fields.

---

## Final Takeaway

The most important finding isn't that Wednesday is the busiest day or that Grove St PATH is the most popular station.

It's that riders aged 75+ logged 2,729 minutes across 57 trips — and **one ride accounts for 2,422 of them.**

Drop that single row and the group's average collapses from 47.9 minutes to 5.5. Nothing else in the dataset changes. 54 of those 57 trips were under ten minutes.

What makes this worth showing is that nothing in the pipeline would have flagged it. The mean computes cleanly. The chart renders. The bar is tall, legible and completely wrong. A slide reading *75+ riders: 48 min average trip* would survive every review it passed through, because the arithmetic is correct at every step.

The only thing that catches it is asking how many rows the number rests on — a question no tool prompts you to ask.

That question costs one line of code. Here it's the difference between a demographic insight and a bike nobody returned.
