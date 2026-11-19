# Global Restaurant Insights: Power BI Dashboard

A two-page Power BI report that cleans raw restaurant data from 15 countries and answers where customer ratings, engagement and pricing can be improved.

![Dashboard](screenshots/dashboard.png)
![Insights](screenshots/insights.png)

## Business problem

A global restaurant chain has collected raw data from outlets around the world. The data is messy and hard to use for decisions.

**Question:** Which cuisines, price ranges and markets drive the best ratings and the most customer engagement, and where should the chain improve pricing and menu offerings?

## Dataset

| Table | Rows | Key columns |
|---|---|---|
| Restaurant details | 9,551 | Restaurant ID, Name, Country Code, City, Cuisines, Average Cost for two, Currency, Price range, Aggregate rating, Votes |
| Country details | 15 | Country Code, Country Name (supplied in a sideways layout) |

## Tools

Power BI Desktop, Power Query, DAX

## Data preparation (Power Query)

1. Transposed the country table into two columns (Country Code, Country Name) and set the data types.
2. Removed extra blank columns from both tables.
3. Merged the country names into the restaurant table on Country Code (Left Outer join).
4. Replaced null cuisines with "All".
5. Duplicated the Cuisines column and split it by comma into Cuisines-1 to Cuisines-4, keeping the original column.
6. Created a Cost Label column (currency symbol + average cost for two), for example `P 1100` or `R$ 55`.
7. Created a rating category column (Poor / Good / Very Good).
8. Built summary queries: average rating and total votes per country, and the top India restaurant by Votes x Rating.

**Data quality notes**
- 2,148 restaurants (22.5%) have a rating of 0. This means "not rated", and 2,139 of them are in India. Treating them as real ratings pulls the overall average down from 3.44 to 2.67.
- The currency label "Botswana Pula(P)" belongs to the Philippines, and the country name is spelled "Phillipines" in the source.

## Data model

One-to-many relationship: Country details (Country Code) to Restaurant details (Country Code), single direction.

## Key DAX

```DAX
Avg Rating (Rated) =
CALCULATE(AVERAGE('restaurant details'[Aggregate rating]), 'restaurant details'[Aggregate rating] > 0)

Unrated % =
DIVIDE(
    CALCULATE(COUNTROWS('restaurant details'), 'restaurant details'[Aggregate rating] = 0),
    COUNTROWS('restaurant details')
)

Rating Group =
SWITCH(
    TRUE(),
    'restaurant details'[Aggregate rating] = 0, "Not Rated",
    'restaurant details'[Aggregate rating] < 3, "Poor",
    'restaurant details'[Aggregate rating] < 4, "Good",
    "Very Good"
)
```

## Dashboard

**Page 1: Dashboard.** KPI cards (Avg Rating, Restaurant Count, Total Votes, Unrated %), votes and rating by country, rating mix, top 10 cuisines, price range vs rating, and slicers for country, price range and rating group.

**Page 2: Insights.** Key findings, recommendations, and a table of popular but low-rated restaurants.

## Key findings

- **22.5% of restaurants are unrated** (2,148 of 9,551), almost all in India. Excluding them, the average rating is 3.44.
- **India is 91% of the data** (8,652 restaurants) and about 79% of all votes, but it has the lowest rated average among the 15 countries (about 3.4). The Philippines (4.47), Turkey and Indonesia (4.30) rate highest.
- **Higher price goes with higher rating:** the average rises from 3.24 (price range 1) to 3.89 (price range 4).
- **Cuisine gap:** Mediterranean (4.0), European (3.96) and Seafood (3.93) rate highest. Mithai (3.14), South Indian (3.24) and Fast Food (3.26) rate lowest. North Indian is the largest category (about 3,000 restaurants).
- **Engagement is concentrated:** the top 1% of restaurants capture about 21% of all votes.
- **Top India restaurant by Votes x Rating:** Toit, Bangalore (10,934 votes x 4.8).
- **Popular but low-rated:** 9 restaurants have at least 1,000 votes and a rating below 3.5, led by My Bar Lounge & Restaurant (2,460 votes, 2.7) and The Wine Company (2,412 votes, 2.4).

## Recommendations

1. Collect ratings for the 2,139 unrated Indian outlets. This is the biggest data gap.
2. Raise quality in high-volume, low-rated cuisines such as North Indian, Chinese and Fast Food.
3. Grow the mid-to-premium price tiers and the highly rated cuisines.
4. Review the popular low performers for service and food quality issues.

## Limitations

- India dominates the sample, so country comparisons rely on small samples outside India (for example, Canada has only 4 restaurants).
- Ratings are customer-reported and do not show change over time.

## Files

- `Gourmet_Restaurant_Insights.pbix`: the Power BI report
- `restaurant_dataset.xlsx`: the source data
- `screenshots/`: images of the two report pages

## Author

Onkar Gupta | [LinkedIn](https://linkedin.com/in/onkar-gupta-66a268293) | [GitHub](https://github.com/onkargupta137-crypto)
