<p align="center">
  <img src="docs/images/00-citibike-banner.jpg" alt="NYC Citi Bike" width="640">
</p>

# NYC Citi Bike — Demand & Rider Behaviour Analysis

> **What can 18,449 bike rides tell us about how people actually use a bike-share system?**

[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas)](https://pandas.pydata.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-orange)](https://matplotlib.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter)](https://jupyter.org/)

I analysed one year of Citi Bike data from the Jersey City service area to look at demand, rider behaviour, trip duration and a few assumptions that the data might tempt us to make.

The dataset contains **20,400 raw records**, which became **18,449 trips** after cleaning, covering **50 stations and 500 bikes** from January to December 2017.

One important note: the numbers below are based on the full cleaned dataset. The notebook removes the single longest trip later in the analysis, so some numbers there will differ slightly.

---

## A few things stood out

### 98.1% of trips are made by subscribers

Citi Bike looks much more like everyday transportation than a weekend leisure service.

Average weekday demand is around **75% higher than weekend demand** — 3,003 vs 1,716 trips per day.

Wednesday is the busiest day with **3,633 trips**.

---

### Demand is concentrated around a few stations

The busiest stations are:

| Station        | Trips |
| -------------- | ----: |
| Grove St PATH  | 2,319 |
| Exchange Place | 1,342 |
| Hamilton Park  | 1,185 |
| Sip Ave        | 1,184 |
| Morris Canal   |   768 |

Grove St PATH alone accounts for **12.6% of all trips**.

The top three stations account for roughly **26% of total demand**.

And there's an interesting pattern here: Grove St PATH and Exchange Place are PATH train stations, while Newport PATH also appears near the top.

That makes sense if Citi Bike is being used for the **first or last part of a commute**.

It also means that simply spreading bikes evenly across the network probably isn't the best way to manage supply. The busy transit stations are where the demand is.

![Station demand](docs/images/01-hero-top-pickup-locations.png)

Worth noting that Hamilton Park and Sip Ave are separated by a single trip — 1,185 against 1,184. Below the top two, that ordering is effectively a tie.

---

## Trip duration is not normally distributed

The average trip is **9.6 minutes**.

The median is **5 minutes**.

That's a pretty big difference.

| Metric  |  Duration |
| ------- | --------: |
| Mean    |   9.6 min |
| Median  |     5 min |
| Minimum |     1 min |
| Maximum | 6,515 min |

The longest trip lasted more than **108 hours**.

There are **103 trips longer than one hour**.

That doesn't necessarily mean 103 people decided to go on extremely long bike rides. Some of these are probably bikes that weren't returned properly or other data-quality issues.

The important part is that a handful of extreme values can have a big effect on the average.

So when looking at trip duration, **the median tells us much more about the typical ride than the mean does.**

![Trip duration distribution](docs/images/07-trip-duration-distribution.png)

---

## Then there is the 75+ group

This is where the data gets interesting.

At first glance, riders aged 75+ look like they take dramatically longer trips than everyone else:

| Age   |         Mean |    Median | Trips |
| ----- | -----------: | --------: | ----: |
| 18–24 |     11.9 min |    10 min |    56 |
| 25–34 |      9.0 min |     6 min | 4,434 |
| 35–44 |     10.3 min |     5 min | 8,377 |
| 45–54 |      8.1 min |     5 min | 3,238 |
| 55–64 |     10.2 min |     6 min | 1,600 |
| 65–74 |      7.4 min |     6 min |   687 |
| 75+   | **47.9 min** | **4 min** |    57 |

The average says **47.9 minutes**.

The median says **4 minutes**.

So what's going on?

One ride.

A single **2,422-minute trip** accounts for **88.8% of all minutes logged by the 75+ group**.

Remove that one row and their average drops from **47.9 minutes to 5.5 minutes**.

And 54 of their 57 trips lasted less than 10 minutes.

![Trip duration by age group, mean versus median](docs/images/06-mean-vs-median-by-age-group.png)

So depending on which statistic you look at, you could tell two completely different stories:

> *"Older riders take much longer trips."*

or

> *"Older riders have the shortest median trip duration."*

The second one is much closer to what is actually happening.

---

## Age doesn't explain trip duration

The correlation between age and trip duration is:

**r = -0.002**

Basically zero.

The scatter plot tells the same story: there is a dense band of short trips across almost every age, with some extreme values scattered around it.

![Age versus trip duration](docs/images/05-age-vs-trip-duration.png)

So there isn't much evidence here that **age is a useful predictor of how long someone rides**.

---

## Who actually uses the system?

The rider base is heavily concentrated between 25 and 54.

**87% of all trips** come from this age range.

The 35–44 group alone accounts for **8,377 trips**.

At the other end, riders under 25 account for only **56 trips**, or 0.3% of the dataset.

![Rentals by age group](docs/images/03-rentals-by-age-group.png)

That's interesting, but the data doesn't tell us *why*.

It could be pricing, awareness, demographics, availability, sampling or something else entirely.

I'd treat it as a question worth investigating, not as an explanation.

---

## Weekdays vs weekends

The weekday/weekend split is pretty clear:

| Day       | Subscriber | One-time |     Total |
| --------- | ---------: | -------: | --------: |
| Monday    |      2,715 |       57 |     2,772 |
| Tuesday   |      2,647 |       30 |     2,677 |
| Wednesday |      3,590 |       43 | **3,633** |
| Thursday  |      3,239 |       20 |     3,259 |
| Friday    |      2,645 |       30 |     2,675 |
| Saturday  |      1,612 |  **102** |     1,714 |
| Sunday    |      1,651 |       68 |     1,719 |

The pattern suggests two fairly different types of usage:

**Weekdays → subscribers / commuting**

**Weekends → lower demand / relatively more casual riders**

Saturday is particularly interesting.

It has the **highest number of one-time users**, even though it has the lowest total number of rides.

Casual riders make up **6.0% of Saturday trips**, compared with just **0.6% on Thursday**.

That's a tenfold difference in the composition of riders.

![Usage by weekday and user type](docs/images/04-rentals-by-usertype-weekday.png)

---

# What would I do with this?

If I were managing the system, a few things would stand out:

### 1. Rebalance around transit stations

The demand isn't evenly distributed.

Grove St PATH and Exchange Place alone generate a huge amount of activity, so those locations deserve more attention than an average station.

### 2. Plan for weekday demand

Weekday demand is roughly **75% higher than weekends**.

That should influence how bikes and operational resources are allocated.

### 3. Look at weekends for casual-user acquisition

Saturday has the highest number and proportion of one-time users.

If the goal is to convert casual riders into subscribers, this looks like a much more interesting segment to investigate.

### 4. Don't build an age strategy around trip duration

The correlation is basically zero.

And the most dramatic result — the 75+ group — disappears once you look at the underlying trips.

**The data isn't strong enough to support that conclusion.**

---

# How I approached it

Nothing particularly fancy.

```text
Raw Data
   ↓
Cleaning
   ↓
Exploration
   ↓
Distributions & Outliers
   ↓
Segmentation
   ↓
Visualisation
   ↓
Conclusions
```

### Cleaning

* Removed duplicates
* Removed rows with missing values
* Converted trip duration to numeric
* Parsed timestamps
* Created weekday/month variables
* Reduced the dataset from **20,400 → 18,449 trips**

### Analysis

I looked at:

* Trip volume
* Station demand
* Weekday vs weekend behaviour
* Subscriber vs one-time users
* Age groups
* Trip duration
* Correlation
* Distributions and outliers

---

## Tech

**Python**
**Pandas** — cleaning and analysis
**Matplotlib** — visualisation
**Jupyter Notebook** — analysis

---

## Repository

```text
nyc-bikeshare-demand-analysis/
│
├── notebook.ipynb
├── New York Citi Bikes_Raw Data - NYCitiBikes.csv
│
└── docs/
    └── images/
```

The notebook contains the full analysis.

```bash
git clone https://github.com/niksaderek/nyc-bikeshare-demand-analysis.git
cd nyc-bikeshare-demand-analysis
pip install pandas matplotlib jupyter
jupyter notebook notebook.ipynb
```

---

# The part I found most interesting

The most important finding isn't that Wednesday is the busiest day or that Grove St PATH is the most popular station.

It's what happened with the 75+ group.

They logged **2,729 minutes across 57 trips**.

One ride accounts for **2,422 of those minutes**.

Drop that one row and the average falls from **47.9 minutes to 5.5**.

Nothing else changes.

54 of those 57 trips were under ten minutes.

And here's the uncomfortable part:

**nothing in the normal analysis would necessarily flag this.**

The average calculates correctly.

The chart renders correctly.

The bar is there.

And a presentation saying *"75+ riders average 48-minute trips"* would look perfectly reasonable.

The only thing that catches it is asking:

**"How many rows is this number actually based on?"**

That question costs one line of code.

Here, it is the difference between an interesting demographic finding and a bike that probably wasn't returned.
