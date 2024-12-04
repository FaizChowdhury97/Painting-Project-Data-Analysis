# Painting-Project-Data-Analysis

# Painting Project Data Analysis

## Project Overview
The Painting Project involves analyzing data for various paintings, artists, canvas sizes, and museum details to understand key metrics that can drive insights into the artwork's performance, trends, and growth over time.

## Data Analysis Goals
1. **Track Artist Performance**: Identify trends and performance metrics for various artists and their works over time.
2. **Evaluate Canvas Sizes**: Analyze how different canvas sizes affect sales and popularity of paintings.
3. **Museum Analysis**: Assess the performance of paintings across different museums to understand regional or venue-specific trends.

## Data Information
The Painting Project database consists of six tables, each serving a specific purpose. These tables are as follows:

### artist
| Field        | Type |
|--------------|------|
| artist_id    | int  |
| full_name    | text |
| first_name   | text |
| middle_names | text |
| last_name    | text |
| nationality  | text |
| style        | text |
| birth        | int  |
| death        | int  |

### canvas_size
| Field    | Type |
|----------|------|
| size_id  | int  |
| width    | int  |
| height   | text |
| label    | text |

### image_link
| Field               | Type  |
|---------------------|-------|
| work_id             | int   |
| url                 | text  |
| thumbnail_small_url | text  |
| thumbnail_large_url | text  |

### museum
| Field   | Type |
|---------|------|
| museum_id | int  |
| name      | text |
| address   | text |
| city      | text |
| state     | text |
| postal    | text |
| country   | text |
| phone     | text |
| url       | text |

### museum_hours
| Field    | Type |
|----------|------|
| museum_id| int  |
| day      | text |
| open     | text |
| close    | text |

### product_size
| Field        | Type |
|--------------|------|
| work_id      | int  |
| size_id      | int  |
| sale_price   | int  |
| regular_price| int  |

### subject
| Field   | Type |
|---------|------|
| work_id | int  |
| subject | text |

### work
| Field     | Type |
|-----------|------|
| work_id   | int  |
| name      | text |
| artist_id | int  |
| style     | text |
| museum_id | int  |

## SQL Queries

### Problem 1: Monthly Trends for Google Search Sessions and Orders

#### SQL Query
```sql
SELECT 
    YEAR(a.created_at) AS year, 
    MONTH(a.created_at) AS month,
    COUNT(DISTINCT a.website_session_id) AS sessions,
    COUNT(DISTINCT b.order_id) AS orders,
    ROUND(COUNT(DISTINCT b.order_id) * 1.0 / COUNT(DISTINCT a.website_session_id) * 100, 2) AS conv_rate
FROM 
    website_sessions a
LEFT JOIN 
    orders b ON b.website_session_id = a.website_session_id
WHERE 
    a.created_at < '2012-11-27'
    AND a.utm_source = 'gsearch'
GROUP BY 
    1, 2;
```

### Query Explanation
- The query groups data by year and month using the `created_at` column.
- It counts distinct sessions and orders, and calculates the conversion rate as orders per session.
- The filter ensures only Google Search sessions (`utm_source = 'gsearch'`) are included.
- Results are grouped by year and month.

### Answer
The query returns the following result:

| Year | Month | Sessions | Orders | Conversion Rate |
|------|-------|----------|--------|-----------------|
| 2012 | 3     | 1860     | 60     | 3.23%           |
| 2012 | 4     | 3574     | 92     | 2.57%           |
| 2012 | 5     | 3410     | 97     | 2.84%           |
| 2012 | 6     | 3578     | 121    | 3.38%           |
| 2012 | 7     | 3811     | 145    | 3.80%           |
| 2012 | 8     | 4877     | 184    | 3.77%           |
| 2012 | 9     | 4491     | 188    | 4.19%           |
| 2012 | 10    | 5534     | 234    | 4.23%           |
| 2012 | 11    | 8889     | 373    | 4.20%           |

### Answer Interpretation
- **Consistent Growth**: Both sessions and orders increased steadily each month.
- **Positive Conversion Rate**: The conversion rate improved month-over-month, indicating an increasing efficiency in converting sessions into orders over time.

## Problem 2
**Objective:** Identify museums that do not have any paintings.

#### SQL Query
```sql
SELECT *
FROM museum
WHERE museum_id NOT IN ( 
    SELECT museum_id
    FROM work
);
```
#### Query Explanation
This query identifies museums that do not have any paintings by checking the `museum` table and excluding museums that have at least one associated painting in the `work` table.

- **Subquery:** The subquery (`SELECT museum_id FROM work`) retrieves all the `museum_id` values where paintings (or works of art) are associated.
- **Main Query:** The main query selects all columns from the `museum` table where the `museum_id` is **not** present in the list of `museum_id` values returned by the subquery.
- **Result:** The query will return the museums that have no paintings or works associated with them.

#### Answer Interpretation
This query will return a list of museums that do not have any paintings or works of art listed in the `work` table.

### Query 3: How many paintings have an asking price of more than their regular price?

```sql
SELECT COUNT(work_id)
FROM product_size 
WHERE sale_price > regular_price
