# Zomato Restaurant Analysis — Power BI Dashboard

An interactive Power BI dashboard analyzing ~9,600 restaurants across 15 countries 
(141 cities) on Zomato, exploring what actually drives restaurant success — rating, 
price, delivery, and table booking — through hypothesis testing and a custom 
weighted success score.

---

## What's inside

### Page 1: Overview
High-level view of the dataset — total restaurants, average rating, rating 
distribution, and city coverage. New Delhi alone accounts for 58% of all restaurants, 
confirming the dataset is heavily India-centric despite spanning 15 countries.

![Overview](overview.png)

### Page 2: Success Factors
Tests whether online delivery and table booking actually correlate with better 
ratings — they do. Table booking shows a 1.0-point rating gap (bigger than any price 
tier difference), and online delivery adds ~0.8 points on average. Also shows 
diminishing returns on price: the rating gap between Premium and Luxury restaurants 
is just 0.1 points.

![Success Factors](success-factor.png)

### Page 3: Cities Deep Dive
Ranks the top 10 cities by a custom **Restaurant Success Score** (60% average rating 
+ 40% normalized vote volume), cross-referenced against restaurant count per city to 
flag small-sample cities that would otherwise skew the ranking.

![Cities Deep Dive](cities-deepdive.png)

---

## Key technical work

-## Key technical work

- **Custom DAX measure — Restaurant Success Score:**
```dax
  Restaurant Success Score = 
  VAR AvgRating = AVERAGE(zomato[Aggregate rating])
  VAR MaxVotes = MAXX(ALL(zomato), zomato[Votes])
  VAR NormVotes = DIVIDE(AVERAGE(zomato[Votes]), MaxVotes, 0)
  RETURN
    (AvgRating * 0.6) + (NormVotes * 5 * 0.4)
```
  A weighted score combining average rating (60%) and vote volume normalized 
  against the city with the highest vote count (40%), scaled to a 0–5 range.

- **Fixed a silent normalization bug:** the first version of this measure wrote 
  `MaxVotes` as `MAXX(ALL(zomato), AVERAGE(zomato[Votes]))` — using `AVERAGE()` 
  inside `MAXX` instead of the raw column. Since `AVERAGE()` ignores row context, 
  every iteration returned the same overall average instead of the true maximum, 
  so `NormVotes` wasn't a real 0–1 ratio and could push scores above the intended 
  scale. Fixing it to `MAXX(ALL(zomato), zomato[Votes])` corrected the scores 
  dataset-wide and changed which cities ranked in the top 10.
  

- **Small-sample flagging:** built a dual-axis chart (Success Score as columns, 
  Restaurant Count as a secondary-axis line) to check the Top 10 cities against 
  their actual sample size. This surfaced that Secunderabad and Panchkula's 
  high scores were based on just 1–2 restaurants each, while the rest of the 
  Top 10 were backed by ~20 — informed the insight text to explicitly flag 
  low-sample cities instead of ranking them at face value.
## Tech stack

Power BI Desktop · DAX · Power Query

## Data source

[Zomato Restaurants dataset](https://www.kaggle.com/datasets/shrutimehta/zomato-restaurants-data/data)(Kaggle) — ~9,600 restaurants, 15 countries, 
141 cities.

---

**[LinkedIn](https://linkedin.com/in/khushi-khandelwal04)** · 
**[More projects](https://github.com/khushikhandelwal04)**
