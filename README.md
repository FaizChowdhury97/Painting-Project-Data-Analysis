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

### Query 1: 
Fetch all the paintings which are not displayed on any museums?

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


### Query 2
Identify museums that do not have any paintings.

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

### Query 3:
How many paintings have an asking price of more than their regular price?

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

### Query 4: 
Identify the paintings whose asking price is less than 50% of its regular price

```sql
SELECT * 
FROM product_size
WHERE sale_price < (0.5 * regular_price)
```
#### Query Explanation
The `WHERE` clause filters the rows to include only those where the `sale_price` is less than half of the `regular_price`. This is achieved by using the condition `sale_price < (0.5 * regular_price)`.

#### Answer Interpretation
The query will return all the rows from the `product_size` table where the asking price of a painting is less than 50% of its regular price. These results highlight paintings being sold at significant discounts, which could provide insights into sales or pricing strategies.

### Query 5: 
Which canvas size costs the most?

```sql
SELECT size_id, MAX(sale_price) AS max_sale
FROM product_size
GROUP BY size_id
ORDER BY max_sale DESC 
LIMIT 1;
```

#### Query Explanation
- **MAX(sale_price):** Retrieves the maximum sale price for each canvas size (`size_id`).
- **GROUP BY size_id:** Groups the data by `size_id` so the maximum price can be calculated for each canvas size.
- **ORDER BY max_sale DESC:** Orders the results in descending order of the maximum sale price.
- **LIMIT 1:** Restricts the output to only the canvas size with the highest maximum sale price.

#### Answer Interpretation
This query identifies the canvas size (`size_id`) with the highest sale price (`max_sale`) across all records in the `product_size` table. The result highlights the most expensive canvas size available for sale, which could provide insights into pricing trends or premium offerings.

### Query 6: 
Delete duplicate records from work, product_size, subject, and image_link tables

```sql
-- For work table
DELETE FROM work
WHERE work_id NOT IN (
    SELECT MIN(work_id)
    FROM work
    GROUP BY name, artist_id, style, museum_id
);

-- For product_size table
DELETE FROM product_size
WHERE work_id NOT IN (
    SELECT MIN(work_id)
    FROM product_size
    GROUP BY size_id, sale_price, regular_price
);

-- For subject table
DELETE FROM subject
WHERE work_id NOT IN (
    SELECT MIN(work_id)
    FROM subject
    GROUP BY work_id, subject
);

-- For image_link table
DELETE FROM image_link
WHERE work_id NOT IN (
    SELECT MIN(work_id)
    FROM image_link
    GROUP BY url, thumbnail_small_url, thumbnail_large_url
);
```
### Query Explanation
1. **DELETE Statement:** Deletes duplicate records from the specified tables.
2. **Subquery with MIN(work_id):** Identifies the lowest `work_id` for each unique combination of grouped fields, ensuring one unique entry is retained for each grouping.
3. **GROUP BY Clause:**
   - For `work`: Groups by `name`, `artist_id`, `style`, and `museum_id`.
   - For `product_size`: Groups by `size_id`, `sale_price`, and `regular_price`.
   - For `subject`: Groups by `work_id` and `subject`.
   - For `image_link`: Groups by `url`, `thumbnail_small_url`, and `thumbnail_large_url`.
4. **NOT IN Clause:** Ensures that only rows not matching the minimum `work_id` in the grouped results are deleted.

### Answer Interpretation
The query removes duplicate rows from the `work`, `product_size`, `subject`, and `image_link` tables while retaining one unique record for each combination of grouped fields. This process ensures data integrity and eliminates redundancy in the database.

### Query 7: Identify the museums with invalid city information in the given dataset

SELECT museum_id, name, city
FROM museum
WHERE city IS NULL
   OR city = ' '
   OR city REGEXP '[^A-Za-z]'
   OR city REGEXP '^[0-9]'
   OR LENGTH(city) < 3;

-- Additional Query
SELECT * 
FROM museum 
WHERE city REGEXP '^[0-9]';

### Query Explanation
1. **Invalid City Checks:**
   - `city IS NULL`: Identifies records where the city field is missing.
   - `city = ' '`: Checks for city fields with only a space.
   - `city REGEXP '[^A-Za-z]'`: Detects cities containing non-alphabetic characters.
   - `city REGEXP '^[0-9]'`: Flags cities starting with numeric characters.
   - `LENGTH(city) < 3`: Ensures cities have at least three characters to be considered valid.

2. **Additional Query:** The second query specifically checks for museums where the city starts with a numeric character using a regular expression.

### Answer Interpretation
The first query returns all museums with invalid city information, considering various criteria like null values, spaces, non-alphabetic characters, or cities starting with numbers. The second query focuses solely on cities that begin with numeric characters, offering a targeted validation check.

### Query 8: Museum_Hours table has 1 invalid entry. Identify it and remove it.

```sql

SET SQL_SAFE_UPDATES = 0;

WITH RankedMuseumHours AS (
    SELECT *, 
           ROW_NUMBER() OVER (PARTITION BY museum_id, day ORDER BY museum_id) AS rn
    FROM museum_hours
)
DELETE FROM museum_hours
WHERE museum_id IN (
    SELECT museum_id
    FROM RankedMuseumHours
    WHERE rn > 1
);
```
### Query Explanation
1. **Identify Duplicate Entries:**
   - **CTE (Common Table Expression):** The `RankedMuseumHours` CTE ranks rows within the `museum_hours` table based on `museum_id` and `day`, assigning a unique `ROW_NUMBER` to each entry in ascending order.
   - **Partition:** `PARTITION BY museum_id, day` groups rows for the same museum and day.
   - **Ranking Logic:** The `ROW_NUMBER` function orders rows, identifying duplicates as those with a rank (`rn`) greater than 1.

2. **Delete Invalid Entry:**
   - The `DELETE` statement removes rows from the `museum_hours` table where the rank (`rn`) is greater than 1, ensuring only one valid record per `museum_id` and `day`.

### Answer Interpretation
The query identifies and removes duplicate or invalid entries in the `museum_hours` table by using a ranking mechanism. After execution:
- Only the first valid entry (based on `ROW_NUMBER`) for each `museum_id` and day remains.
- Duplicate rows, including the invalid one, are removed.

### Query 9:
Fetch the top 10 most famous painting subjects

```sql
SELECT * 
FROM (
    SELECT 
        s.subject, 
        COUNT(1) AS no_of_paintings,
        RANK() OVER (ORDER BY COUNT(1) DESC) AS ranking
    FROM work w
    JOIN subject s 
        ON s.work_id = w.work_id
    GROUP BY s.subject
) x
WHERE ranking <= 10;
```


