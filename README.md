# Painting Project Data Analysis

## Project Overview
The Painting Project involves analyzing data for various paintings, artists, canvas sizes, and museum details to understand key metrics that can drive insights into the artwork's performance, trends, and growth over time.

## Data Analysis Goals
1. **Track Artist Performance**: Identify trends and performance metrics for various artists and their works over time.
2. **Evaluate Canvas Sizes**: Analyze how different canvas sizes affect sales and popularity of paintings.
3. **Museum Analysis**: Assess the performance of paintings across different museums to understand regional or venue-specific trends.

## Data Information
The Painting Project database consists of six tables, each serving a specific purpose. These tables are as follows:

1. **artist**
   - `artist_id` (int): Unique identifier for each artist.
   - `full_name` (text): The full name of the artist.
   - `first_name` (text): The artist's first name.
   - `middle_names` (text): Any middle names the artist may have.
   - `last_name` (text): The artist's last name.
   - `nationality` (text): The nationality of the artist.
   - `style` (text): The artistic style of the artist.
   - `birth` (int): The birth year of the artist.
   - `death` (int): The death year of the artist.

2. **canvas_size**
   - `size_id` (int): Unique identifier for each canvas size.
   - `width` (int): The width of the canvas.
   - `height` (text): The height of the canvas.
   - `label` (text): A label or description of the canvas size.

3. **image_link**
   - `work_id` (int): The unique identifier for the painting/work.
   - `url` (text): The URL to the full-size image of the painting.
   - `thumbnail_small_url` (text): The URL to the small-sized thumbnail image.
   - `thumbnail_large_url` (text): The URL to the large-sized thumbnail image.

4. **museum**
   - `museum_id` (int): Unique identifier for each museum.
   - `name` (text): The name of the museum.
   - `address` (text): The address of the museum.
   - `city` (text): The city where the museum is located.
   - `state` (text): The state where the museum is located.
   - `postal` (text): The postal code of the museum's location.
   - `country` (text): The country where the museum is located.
   - `phone` (text): The phone number for the museum.
   - `url` (text): The website URL for the museum.

5. **museum_hours**
   - `museum_id` (int): Foreign key to link the museum's hours to a specific museum.
   - `day` (text): The day of the week (e.g., Monday, Tuesday).
   - `open` (text): The opening time for the museum on the given day.
   - `close` (text): The closing time for the museum on the given day.

6. **product_size**
   - `work_id` (int): Foreign key linking to a specific work of art (painting).
   - `size_id` (int): Foreign key linking to the canvas size.
   - `sale_price` (int): The sale price of the painting.
   - `regular_price` (int): The regular price of the painting.

7. **subject**
   - `work_id` (int): Foreign key linking to a specific painting or work.
   - `subject` (text): The subject of the painting or work.

8. **work**
   - `work_id` (int): Unique identifier for each work of art.
   - `name` (text): The name of the painting or work of art.
   - `artist_id` (int): Foreign key linking to the artist of the painting.
   - `style` (text): The style of the work.
   - `museum_id` (int): Foreign key linking to the museum where the painting is displayed.


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

---

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

---

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

---

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

---

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

---

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

---

### Query 7: 
Identify the museums with invalid city information in the given dataset

```sql
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
```
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

---

### Query 8: 
Museum_Hours table has 1 invalid entry. Identify it and remove it.

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

---

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
### Query Explanation
1. **Inner Query:**
   - **Aggregation:** The `COUNT(1)` function counts the number of paintings associated with each subject in the `subject` table.
   - **Ranking:** The `RANK()` function ranks subjects based on the number of paintings in descending order (`ORDER BY COUNT(1) DESC`).
   - **Grouping:** The `GROUP BY s.subject` groups records by subject to calculate their respective counts and rankings.

2. **Outer Query:**
   - Filters results to include only the top 10 ranked subjects (`ranking <= 10`).

### Answer Interpretation
This query identifies the 10 most popular painting subjects based on the number of paintings associated with each subject. The results provide:
- **Subject:** Name of the painting subject.
- **no_of_paintings:** Count of paintings associated with the subject.
- **ranking:** The rank of the subject based on its popularity.
This analysis helps highlight the most frequently depicted themes or topics in the dataset.
---

### Query 10:
Identify the museums which are open on both Sunday and Monday. Display museum name, city.
```sql
SELECT m.name, m.city
FROM museum m
INNER JOIN museum_hours h1
    ON m.museum_id = h1.museum_id
INNER JOIN museum_hours h2
    ON m.museum_id = h2.museum_id
WHERE h1.day = 'sunday'
  AND h2.day = 'monday';
```
### Query Explanation
1. **Joins:**
   - Two self-joins on the `museum_hours` table (`h1` and `h2`) are performed to check museum operating hours for different days (Sunday and Monday).
   - These joins are linked through the `museum_id`.

2. **Conditions:**
   - `h1.day = 'sunday'` ensures the museum is open on Sunday.
   - `h2.day = 'monday'` ensures the museum is also open on Monday.

3. **Selection:**
   - `m.name` and `m.city` are selected to display the museum's name and location.

### Answer Interpretation
The query outputs the names and cities of museums that are open on both Sunday and Monday. This information is useful for visitors or analysts interested in museums with flexible or extended opening schedules.

---

### Query 11:
11) How many museums are open every single day?

```sql
SELECT count(*)
FROM (
    SELECT museum_id, COUNT(*)
    FROM museum_hours
    GROUP BY museum_id
    HAVING COUNT(day) = 7
) x;
```
### Query Explanation
1. **Inner Query:**
   - The inner query counts the number of days each museum is open by grouping the `museum_hours` table by `museum_id`.
   - The `HAVING COUNT(day) = 7` condition ensures that only museums open for all seven days of the week are selected.

2. **Outer Query:**
   - The outer query counts the number of museums (i.e., `museum_id`) returned by the inner query, providing the total number of museums open every day of the week.

### Answer Interpretation
The query returns the count of museums that are open every single day of the week. This helps identify the museums with the most flexible opening hours, which could be beneficial for visitor planning or operational analysis.

---

### Query 13: 
Who are the top 5 most popular artist? (Popularity is defined based on most no of paintings done by an artist)

```sql
SELECT a.artist_id, a.full_name, COUNT(w.work_id) AS number_of_work
FROM artist a
INNER JOIN work w ON a.artist_id = w.artist_id
GROUP BY a.artist_id, a.full_name
HAVING COUNT(w.work_id) >= (
    SELECT COUNT(w2.work_id)
    FROM work w2
    GROUP BY w2.artist_id
    ORDER BY COUNT(w2.work_id) DESC
    LIMIT 1 OFFSET 4  
)
ORDER BY number_of_work DESC;
```
### Query Explanation
1. **Inner Subquery:**
   - The subquery calculates the number of paintings for each artist by counting `work_id` in the `work` table, grouped by `artist_id`.
   - The `ORDER BY COUNT(w2.work_id) DESC` orders artists by the number of works in descending order.
   - `LIMIT 1 OFFSET 4` ensures that the subquery returns the count of paintings for the 5th most prolific artist.

2. **Outer Query:**
   - The outer query retrieves the artist's `artist_id`, `full_name`, and counts the number of paintings they created.
   - It filters for artists whose count of paintings is greater than or equal to the number of paintings of the 5th most prolific artist.

3. **Final Output:**
   - The query orders the results by the number of works in descending order, displaying the top 5 most popular artists.

### Answer Interpretation
The query will return the top 5 artists who have created the most paintings, providing insights into the most prolific contributors to the collection in terms of the number of works produced.

---

### Query 14:
Display the 3 least popular canva sizes

```sql
WITH SizePopularity AS (
    SELECT c.size_id, COUNT(p.work_id) AS number_of_size
    FROM canvas_size c
    LEFT JOIN product_size p ON c.size_id = p.size_id
    GROUP BY c.size_id
),
RankedSizes AS (
    SELECT size_id, number_of_size,
           DENSE_RANK() OVER (ORDER BY number_of_size ASC) AS ranking
    FROM SizePopularity
)
SELECT size_id, number_of_size
FROM RankedSizes
WHERE ranking <= 3;
```

### Query Explanation
1. **Inner Subquery:**
   - The subquery calculates the number of paintings for each artist by counting `work_id` in the `work` table, grouped by `artist_id`.
   - The `ORDER BY COUNT(w2.work_id) DESC` orders artists by the number of works in descending order.
   - `LIMIT 1 OFFSET 4` ensures that the subquery returns the count of paintings for the 5th most prolific artist.

2. **Outer Query:**
   - The outer query retrieves the artist's `artist_id`, `full_name`, and counts the number of paintings they created.
   - It filters for artists whose count of paintings is greater than or equal to the number of paintings of the 5th most prolific artist.

3. **Final Output:**
   - The query orders the results by the number of works in descending order, displaying the top 5 most popular artists.

### Answer Interpretation
The query will return the top 5 artists who have created the most paintings, providing insights into the most prolific contributors to the collection in terms of the number of works produced.

---

### Query 15:
Which museum is open for the longest during a day. Dispay museum name, state and hours open and which day?
```sql
SELECT m.name AS museum_name, 
       m.state AS city, 
       mh.day, 
       mh.open, 
       mh.close, 
       TIME_FORMAT(TIMEDIFF(STR_TO_DATE(mh.close, '%l:%i %p'), STR_TO_DATE(mh.open, '%l:%i %p')), '%H:%i') AS duration
FROM museum m
INNER JOIN museum_hours mh ON m.museum_id = mh.museum_id
ORDER BY TIMEDIFF(STR_TO_DATE(mh.close, '%l:%i %p'), STR_TO_DATE(mh.open, '%l:%i %p')) DESC
LIMIT 1;
```
### Query Explanation
1. **Joining Tables:**
   - The query joins the `museum` table with the `museum_hours` table on the `museum_id` field. This allows us to access both the museum's details and its operating hours.

2. **Calculating Duration:**
   - The `STR_TO_DATE()` function is used to convert the `open` and `close` times into time format.
   - The `TIMEDIFF()` function calculates the difference between the closing time (`close`) and the opening time (`open`), resulting in the duration the museum is open.
   - The `TIME_FORMAT()` function formats the result into an hour and minute format (`%H:%i`).

3. **Ordering and Limiting Results:**
   - The `ORDER BY` clause orders the results by the duration (calculated time difference) in descending order to ensure the longest duration is at the top.
   - `LIMIT 1` restricts the result to the museum with the longest duration of being open on a given day.

### Answer Interpretation
This query will return the museum that has the longest hours of operation on a particular day, along with the specific day, open hours, and the duration it remains open.

---

### Query 16: Which museum has the most no of most popular painting style?

```sql 
WITH popular_style AS
                   (
                    SELECT style, COUNT(*) AS popular_rating,
					DENSE_RANK () OVER ( ORDER BY COUNT(*) DESC) AS ranking
                    FROM work
                    GROUP BY style
				   ),
 CTE AS (  
       SELECT w.museum_id, m.name, p.style, COUNT(1) AS popular_rating
	   FROM work w
       INNER JOIN museum m ON w.museum_id = m.museum_id
       INNER JOIN popular_style p ON w.style = p.style
       WHERE w.museum_id is not null
	     AND p.ranking = 1
		GROUP BY w.museum_id, m.name, p.style)

 SELECT name, style, popular_rating
 FROM CTE
 ORDER BY popular_rating DESC
LIMIT 1;
```
### Query Explanation
1. **Identifying the Popular Style:**
   - The first CTE (`popular_style`) calculates the popularity of each painting style by counting the number of occurrences of each style in the `work` table. The `DENSE_RANK()` function is used to rank the styles by their popularity in descending order.

2. **Finding the Most Popular Style in Each Museum:**
   - The second CTE (`CTE`) joins the `work` table with the `museum` table and the previously defined `popular_style` CTE to filter out the works that match the most popular style (where `ranking = 1`).
   - It then counts the number of paintings of the most popular style in each museum.

3. **Final Selection:**
   - The outer query selects the museum name, the most popular painting style, and the count of those paintings in each museum.
   - The `ORDER BY popular_rating DESC` sorts the results to find the museum with the highest count of the most popular style.
   - `LIMIT 1` restricts the result to the museum with the most paintings of the most popular style.

### Answer Interpretation
This query will return the name of the museum, the most popular painting style, and the number of paintings of that style in the museum. The result highlights the museum that holds the largest collection of the most popular painting style based on the dataset.

---

### Query 17: 
Identify the artists whose paintings are displayed in multiple countries               
```sql
WITH paints AS (
      SELECT a.full_name AS artist_name, w.work_id, w.museum_id
         FROM artist a
		 INNER JOIN work w
         ON a.artist_id = w.artist_id)
         
SELECT p.artist_name, p.work_id, m.country, COUNT(DISTINCT m.country) AS num_countries
FROM museum m
INNER JOIN paints p
ON m.museum_id = p.museum_id
GROUP BY p.artist_name, p.work_id, m.country
HAVING COUNT(m.country) > 1
```
### Query Explanation
1. **Creating Paints CTE:**
   - The `WITH paints` clause creates a Common Table Expression (CTE) that joins the `artist` and `work` tables. It retrieves the artist's name, the work ID, and the museum ID where each artwork is displayed. This allows us to track which artist's works are displayed in which museum.

2. **Join with Museum Table:**
   - The main query joins the `paints` CTE with the `museum` table based on `museum_id` to associate each artwork with the country of the museum.

3. **Counting Distinct Countries:**
   - The `COUNT(DISTINCT m.country)` counts the number of unique countries where each painting (work_id) is displayed. This helps identify whether an artwork is displayed in multiple countries.

4. **Grouping by Artist and Work:**
   - The `GROUP BY` clause groups the data by `artist_name`, `work_id`, and `country` to ensure that we can count the number of countries for each painting.

5. **Filtering Results:**
   - The `HAVING` clause filters the results to only include paintings that are displayed in more than one country, ensuring that only artists with paintings in multiple countries are selected.

### Answer Interpretation
The query will return the artist's name, the work ID, the country where the artwork is displayed, and the number of distinct countries where that work is displayed. It specifically identifies artists whose paintings are shown in multiple countries.

---

### Query 18:
Display the country and the city with most no of museums. Output 2 seperate columns to mention the city and country. If there are multiple value, seperate them with comma.
```sql
WITH country_with_most_museum AS (
    SELECT country, COUNT(*) AS number_of_museum_by_country,
           DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS ranking
    FROM museum
    GROUP BY country
),
city_with_most_museum AS (
    SELECT city, COUNT(*) AS number_of_museum_by_city,
           DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS ranking
    FROM museum
    GROUP BY city
)
SELECT 
    GROUP_CONCAT(DISTINCT country SEPARATOR ', ') AS countries_with_most_museums,
    GROUP_CONCAT(DISTINCT city SEPARATOR ', ') AS cities_with_most_museums
FROM 
    (SELECT country FROM country_with_most_museum WHERE ranking = 1) AS top_countries,
    (SELECT city FROM city_with_most_museum WHERE ranking = 1) AS top_cities;
```
### Query Explanation
1. **Common Table Expressions (CTEs):**
   - The first CTE (`country_with_most_museum`) calculates the number of museums per country and ranks them by the highest count. It uses `DENSE_RANK()` to handle ties in the number of museums.
   - The second CTE (`city_with_most_museum`) calculates the number of museums per city and ranks them similarly.

2. **Selecting Top Countries and Cities:**
   - The main query retrieves the countries and cities with the highest number of museums by selecting those with the rank equal to 1 from both CTEs.
   
3. **Concatenating Results:**
   - `GROUP_CONCAT(DISTINCT country SEPARATOR ', ')` and `GROUP_CONCAT(DISTINCT city SEPARATOR ', ')` are used to concatenate the top countries and cities into comma-separated lists. This ensures that if there are multiple countries or cities with the highest count, they are displayed together.

4. **Final Output:**
   - The result will return two columns: one for the countries with the most museums (`countries_with_most_museums`) and one for the cities with the most museums (`cities_with_most_museums`). If there are ties, multiple values are listed in each column.

### Answer Interpretation
The query will return the countries and cities with the highest number of museums. If there are multiple countries or cities with the same highest number of museums, they will be displayed in comma-separated lists.

---

### Query 19: 
Identify the artist and the museum where the most expensive and least expensive painting is placed. Display the artist name, sale_price, painting name, museum name, museum city and canvas label.

```sql
WITH expensive_artist AS (
    SELECT a.full_name AS artist_name, w.artist_id, w.work_id, w.museum_id, w.name AS painting_name, p.size_id, p.sale_price,
           DENSE_RANK() OVER (ORDER BY p.sale_price DESC) AS ranking1,   -- Rank by most expensive
           DENSE_RANK() OVER (ORDER BY p.sale_price ASC) AS ranking2     -- Rank by least expensive
    FROM work w
    INNER JOIN product_size p ON w.work_id = p.work_id
    INNER JOIN artist a ON w.artist_id = a.artist_id
),
top_museum AS (
    SELECT m.name AS museum_name, m.city, c.label AS canvas_label, 
           e.artist_name, e.artist_id, e.work_id, e.museum_id, e.painting_name, e.size_id, e.sale_price, e.ranking1, e.ranking2
    FROM museum m
    INNER JOIN expensive_artist e ON m.museum_id = e.museum_id
    INNER JOIN canvas_size c ON c.size_id = e.size_id
)
SELECT artist_name, painting_name, sale_price, museum_name, city, canvas_label
FROM top_museum
WHERE ranking1 = 1 OR ranking2 = 1; 
```
### Query Explanation
1. **Common Table Expressions (CTEs):**
   - The first CTE (`expensive_artist`) retrieves details of the paintings, including the artist's name, the painting's name, the sale price, and ranks them by price. The paintings are ranked in two ways: `ranking1` for the most expensive (sorted in descending order) and `ranking2` for the least expensive (sorted in ascending order).
   - The second CTE (`top_museum`) joins the museum data with the ranked paintings, bringing in the museum name, city, and canvas label, along with the ranked paintings from the first CTE.

2. **Selecting Results:**
   - The main query selects the artist name, painting name, sale price, museum name, city, and canvas label from the `top_museum` CTE.
   - It filters the rows to include only those where either the most expensive or least expensive painting is found (`ranking1 = 1 OR ranking2 = 1`).

### Answer Interpretation
The query will return the artist and museum details for both the most expensive and least expensive paintings, along with the sale price, museum city, and canvas label. The results highlight the contrast between high and low-value paintings displayed in museums.

---

### Query 20
Which country has the 5th highest no of paintings?
``sql
WITH highest_painting AS ( 
							SELECT museum_id, COUNT(work_id) AS no_of_painting,
                            DENSE_Rank() OVER (ORDER BY COUNT(work_id) DESC) AS ranking
                            FROM work 
                            GROUP BY museum_id
                            )
SELECT Country, no_of_painting
FROM museum m
INNER JOIN highest_painting h
ON m.museum_id = h.museum_id
WHERE h.ranking = 5
```
### Query Explanation
1. **Common Table Expression (CTE):**
   - The `highest_painting` CTE calculates the number of paintings (`no_of_painting`) associated with each museum by counting the `work_id` in the `work` table.
   - It also ranks the museums based on the number of paintings using the `DENSE_RANK()` function, which orders the museums in descending order of their number of paintings.

2. **Main Query:**
   - The main query joins the `museum` table with the `highest_painting` CTE on `museum_id` to retrieve the country and the corresponding number of paintings.
   - The `WHERE h.ranking = 5` condition filters the results to only return the museum(s) with the 5th highest number of paintings.

### Answer Interpretation
The query will return the country and the number of paintings associated with the museum that has the 5th highest number of paintings. This result helps in identifying the country with a significant but not the highest concentration of artworks.

---

### Query 21: 
Which are the 3 most popular and 3 least popular painting styles?
```sql
WITH cte AS (
    SELECT style, 
           COUNT(1) AS cnt,
           RANK() OVER (ORDER BY COUNT(1) DESC) AS rnk
    FROM work
    WHERE style IS NOT NULL
    GROUP BY style
),
-- Calculate total number of styles
total_count AS (
    SELECT COUNT(DISTINCT style) AS no_of_records
    FROM work
    WHERE style IS NOT NULL
)
SELECT cte.style,
       CASE 
           WHEN cte.rnk <= 3 THEN 'Most Popular' 
           ELSE 'Least Popular' 
       END AS remarks
FROM cte
JOIN total_count
-- This ensures we capture the top 3 most popular and bottom 3 least popular
WHERE cte.rnk <= 3 OR cte.rnk > (total_count.no_of_records - 3);
```
### Query Explanation
1. **Common Table Expression (CTE):**
   - The `highest_painting` CTE calculates the number of paintings (`no_of_painting`) associated with each museum by counting the `work_id` in the `work` table.
   - It also ranks the museums based on the number of paintings using the `DENSE_RANK()` function, which orders the museums in descending order of their number of paintings.

2. **Main Query:**
   - The main query joins the `museum` table with the `highest_painting` CTE on `museum_id` to retrieve the country and the corresponding number of paintings.
   - The `WHERE h.ranking = 5` condition filters the results to only return the museum(s) with the 5th highest number of paintings.

### Answer Interpretation
The query will return the country and the number of paintings associated with the museum that has the 5th highest number of paintings. This result helps in identifying the country with a significant but not the highest concentration of artworks.

---

### Query 22:
Which artist has the most no portrait paintings outside the USA?. Display the artist's name, no of paintings, and the artist's nationality.

```sql
WITH top_artist AS (
                    SELECT 
                    a.full_name AS artist_name,
                    COUNT(w.work_id) AS number_of_portraits,
                    a.nationality,
                    DENSE_RANK() OVER (ORDER BY COUNT(w.work_id) DESC ) AS ranking
                    FROM artist a
                    INNER JOIN work w ON a.artist_id = w.artist_id
                    INNER JOIN museum m ON w.museum_id = m.museum_id
                    WHERE m.country NOT IN ('USA')
                    GROUP BY artist_name, a.nationality)

SELECT artist_name,
       number_of_portraits,
       nationality
FROM top_artist
WHERE ranking = 1

### Query Explanation
1. **Common Table Expression (CTE):**
   - The `top_artist` CTE calculates the total number of portraits (`number_of_portraits`) by each artist outside the USA.
   - The `DENSE_RANK()` function is used to rank the artists in descending order of the number of portraits they have, based on museums located outside the USA.
   - The `WHERE m.country NOT IN ('USA')` condition filters out any paintings displayed in museums located in the USA.

2. **Main Query:**
   - The main query selects the artist's name (`artist_name`), the number of portraits (`number_of_portraits`), and the nationality (`nationality`) of the artist, where the ranking is equal to 1, meaning the artist with the most portraits outside the USA.

### Answer Interpretation
The query will return the name, number of paintings, and nationality of the artist who has the highest number of portrait paintings displayed in museums outside of the USA. This helps identify prominent artists whose work is most widely displayed globally, excluding the USA.
```
