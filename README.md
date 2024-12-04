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

## Query 1: Fetch all the paintings which are not displayed on any museums?

```sql
SELECT p.work_id
FROM product_size p
LEFT JOIN work w
ON p.work_id = w.work_id
WHERE w.museum_id IS NULL;
```
#### Query Explanation
This query retrieves all the paintings (identified by `work_id`) that are not currently displayed in any museums.

- **LEFT JOIN:** The query uses a `LEFT JOIN` to join the `product_size` table with the `work` table on the `work_id` field. This ensures that all paintings from the `product_size` table are included, even if they do not have a corresponding entry in the `work` table.
- **Condition:** The `WHERE` clause filters for rows where the `museum_id` is `NULL`, meaning the painting is not associated with any museum.

#### Answer Interpretation
The query will return a list of paintings (by `work_id`) that are not currently displayed in any museum.


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
```

#### Query Explanation
This query counts the number of paintings that have an asking price (sale price) greater than their regular price.

- **Condition:** The `WHERE` clause filters the rows to only include those where the `sale_price` is greater than the `regular_price`.
- **COUNT:** The `COUNT` function is used to return the total number of `work_id` entries that meet this condition, effectively counting the number of paintings with an asking price higher than the regular price.

#### Answer Interpretation
The query will return the number of paintings where the asking price (sale price) exceeds the regular price.

