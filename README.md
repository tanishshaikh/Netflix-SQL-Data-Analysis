# Netflix-SQL-Data-Analysis
![](https://github.com/najirh/netflix_sql_project/blob/main/logo.png)

## Overview

This project demonstrates advanced SQL techniques to analyze Netflix content and solve business-critical problems. By querying a dataset of movies and TV shows, the analysis addresses key questions related to content distribution, ratings, genres, and geographical trends. These insights can support strategic decision-making for content planning and platform optimization.

---

## Project Objectives

The primary goals of this SQL analysis are to:
1. **Understand Content Distribution**: Analyze the proportion of movies versus TV shows.
2. **Identify Rating Trends**: Determine the most common ratings by content type.
3. **Track Content Release Trends**: Explore data on movies and TV shows released in specific years or months.
4. **Highlight Geographical Distribution**: Identify top-performing countries in terms of content volume.
5. **Discover Popular Genres**: Pinpoint the most frequently featured genres in the dataset.
6. **Support Business Decisions**: Provide actionable insights for content curation and market targeting.

---

## Features and Queries

### **1. Content Type Distribution**
- **Objective**: Compare the number of movies and TV shows available on Netflix.
- **Query**:
  ```sql
  SELECT 
      type,
      COUNT(*) AS content_count
  FROM netflix
  GROUP BY type;
  ```
- **Business Insight**: Helps in identifying whether Netflix focuses more on movies or TV shows.

---

### **2. Most Common Ratings**
- **Objective**: Find the most frequent ratings for both movies and TV shows.
- **Query**:
  - Uses `WITH` clauses for Common Table Expressions (CTEs) to count and rank ratings by content type.
  ```sql
  WITH RatingCounts AS (
      SELECT 
          type,
          rating,
          COUNT(*) AS rating_count
      FROM netflix
      GROUP BY type, rating
  ),
  RankedRatings AS (
      SELECT 
          type,
          rating,
          rating_count,
          RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
      FROM RatingCounts
  )
  SELECT 
      type,
      rating AS most_frequent_rating
  FROM RankedRatings
  WHERE rank = 1;
  ```
- **Business Insight**: Understand audience preferences and optimize content for target ratings.

---

### **3. Yearly Content Analysis**
- **Objective**: Retrieve all content released in a specific year, such as 2020.
- **Query**:
  ```sql
  SELECT * 
  FROM netflix
  WHERE release_year = 2020;
  ```
- **Business Insight**: Analyze production or acquisition trends for specific years.

---

### **4. Top 5 Countries by Content Volume**
- **Objective**: Determine the countries contributing the most content to Netflix.
- **Query**:
  - Splits multi-valued fields using `UNNEST()` and aggregates the data.
  ```sql
  SELECT 
      country,
      COUNT(*) AS total_content
  FROM (
      SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS country
      FROM netflix
  ) AS country_data
  GROUP BY country
  ORDER BY total_content DESC
  LIMIT 5;
  ```
- **Business Insight**: Identify high-performing regions for content production and localization strategies.

---

### **5. Most Popular Genres**
- **Objective**: Rank genres based on their frequency in the dataset.
- **Query**:
  ```sql
  SELECT 
      genre,
      COUNT(*) AS genre_count
  FROM netflix
  GROUP BY genre
  ORDER BY genre_count DESC;
  ```
- **Business Insight**: Highlight trending genres to align content offerings with audience preferences.

---

### **6. Monthly Content Trends**
- **Objective**: Analyze the volume of content added during specific months.
- **Query**:
  ```sql
  SELECT 
      EXTRACT(MONTH FROM date_added) AS month,
      COUNT(*) AS total_content
  FROM netflix
  GROUP BY month
  ORDER BY total_content DESC;
  ```
- **Business Insight**: Understand seasonal trends in content addition to align marketing campaigns.

---

## Technical Highlights

- **SQL Techniques Used**:
  - Common Table Expressions (`WITH` clause).
  - Window Functions (`RANK()` and `PARTITION BY`).
  - String Manipulation (`STRING_TO_ARRAY`, `UNNEST()`).
  - Aggregate Functions (`COUNT()`, `SUM()`).
  - Filtering and Sorting (`WHERE`, `ORDER BY`).

- **Dataset Structure**:
  - `type`: Indicates whether the content is a movie or TV show.
  - `rating`: Provides content ratings (e.g., PG-13, TV-MA).
  - `release_year`: Year of release.
  - `country`: Countries where the content was produced.
  - `date_added`: Date the content was added to Netflix.

---

## Applications and Use Cases

1. **Content Strategy**: Optimize content acquisition and production strategies by understanding trends.
2. **Market Expansion**: Target specific countries or regions based on content volume and popularity.
3. **Genre Focus**: Align new content development with audience-preferred genres.
4. **Rating Trends**: Guide parental controls or regional rating compliance using common rating insights.

---

## Future Enhancements

1. **Incorporate Viewer Data**:
   - Integrate user engagement metrics to correlate content popularity with viewer behavior.
2. **Time-Series Analysis**:
   - Extend queries to analyze content trends over years or quarters.
3. **Dynamic Reporting**:
   - Develop stored procedures or SQL views for real-time reporting.
