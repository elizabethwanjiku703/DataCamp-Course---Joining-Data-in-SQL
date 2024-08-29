## CHAPTER 3
### Set Theories for SQL joins
#### -- UNION vs UNION ALL
###### **What result will the following SQL query produce?**
```SQL
SELECT *
FROM languages
UNION
SELECT *
FROM currencies;
```
*The results show an error because languages and currencies have different column fields.* 

###### **What result will the following SQL query produce?**
```SQL
SELECT code FROM
languages
UNION ALL
SELECT code FROM 
currencies;
```
*The results show an ordered list of each country code in languages and currencies including duplicates.*
###### **What will the following SQL query produce?**
```SQL
SELECT code
FROM languages
UNION
SELECT curr_id
FROM currencies;
```
*A SQL error will be produced because code and curr_id are not of the same data type*
> [!Note]
> Both questions on the left and right sides of the operation must have the same types of data. The names of the fields can be different, but the results will always show the field names from the left question.
### **--- Comparing global economies**
```SQL
#### **Questions**
1. Begin your query by selecting all fields from economies2015.
2. Create a second query that selects all fields from economies2019.
3. Perform a set operation to combine the two queries you just created, ensuring you do not return duplicates.
   
SELECT * 
FROM economies2015
-- Set operation
UNION

SELECT *
FROM economies2019
ORDER BY code, year;
```

### **--Comparing two set operations**
- Instructions 1
```SQL
-- Perform an appropriate set operation that determines all pairs of country code and year (in that order) from economies and populations, excluding duplicates. Order by country code and year.
SELECT code, year
FROM economies
-- Set theory clause
UNION
SELECT country_code, year
FROM populations
ORDER BY code, year;
```
- Instructions 2
```SQL
-- Amend the query to return all combinations (including duplicates) of country code and year in the economies or the populations tables.
SELECT code, year
FROM economies
-- Set theory clause
UNION ALL
SELECT country_code, year
FROM populations
ORDER BY code, year;
```
> [!Important]
> **UNION ALL** shows all the results, including **any duplicates**.
> **UNION** only shows the **results once**, even if the information is repeated. It **removes any duplicates**.

### **--INTERSECT**
```SQL
-- Return all cities with the same name as a country
SELECT name
FROM cities
INTERSECT
SELECT name
FROM countries;
```

### **--EXCEPT**
```SQL
-- Return all cities that do not have the same name as a country
SELECT c.name
FROM cities AS c
EXCEPT
SELECT c.name
FROM countries AS c
ORDER BY name;
```

## CHAPTER 4
### Subqueries
```SQL
-- Re-order the lines of code provided to find all records from economies2019 where gross_savings in economies2015 were
below the 2015 global average, using code to filter the economies2019 records.

SELECT *
FROM economies2019
WHERE code in
   (
   SELECT code
   FROM economies2015
   WHERE gross_savings < 22.5
   )
```
- Instructions 1
```SQL
-- Select country code as a single field from the countries table, filtering for countries in the 'Middle East' region.
SELECT code
FROM countries
WHERE region = 'Middle East';
```

- Instruction 2
```SQL
-- Write a second query to SELECT the name of each unique language appearing in the languages table; do not use column aliases here.
-- Order the result set by name in ascending order.
SELECT DISTINCT name
FROM languages
ORDER BY name;
```

- Instruction 3
```SQL
-- Create a semi-join out of the two queries you've written, which filters unique languages returned in the first query for only those languages spoken in the 'Middle East'.
SELECT DISTINCT name
FROM languages
-- Add syntax to use the bracketed subquery below as a filter
WHERE code IN
    (SELECT code
    FROM countries
    WHERE region = 'Middle East')
ORDER BY name;
```
-- Begin by writing a query to return the code and name (in order, not aliased) for all countries in the continent of Oceania from the countries table.
-- Observe the number of records returned and compare this with the provided INNER JOIN, which returns 15 records.
```SQL
SELECT c1.code, name
FROM countries AS c1
WHERE c1.continent = 'Oceania';
```
-- Now, build on your query to complete your **anti join**, by adding an additional 
-- filter to return every country code that is not included in the currencies table.
```SQL
SELECT code, name
FROM countries
WHERE continent = 'Oceania'
-- Filter for countries not included in the bracketed subquery
AND code NOT IN 
    (SELECT code
    FROM currencies);
```
### Subqueries inside WHERE and SELECT
-- Subquery inside WHERE
```SQL
-- Begin by calculating the average life expectancy from the populations table.
-- Filter your answer to use records from 2015 only.

SELECT AVG(life_expectancy)
FROM populations
WHERE year = 2015;

-- The answer from your query has now been nested into another query;
-- use this calculation to filter populations for all records where life_expectancy is 1.15 times higher than average.

SELECT *
FROM populations
WHERE life_expectancy > 1.15 * 
  (SELECT AVG(life_expectancy)
   FROM populations
   WHERE year = 2015) 
    AND year = 2015;
```
##### -- Return the name, country_code and urbanarea_pop for all capital cities (not aliased).
```SQL
-- Select relevant fields from cities table
SELECT city.name, city.country_code, city.urbanarea_pop
FROM cities AS city
-- Filter using a subquery on the countries table
WHERE name IN
(
    SELECT capital
    FROM countries
    )
ORDER BY urbanarea_pop DESC;
```
### Subquery inside SELECT
##### -- Write a LEFT JOIN with countries on the left and the cities on the right, joining on country code.
##### -- In the SELECT statement of your join, include country names as country, and count the cities in each country, aliased as cities_num.
##### -- Sort by cities_num (descending), and country (ascending), limiting to the first nine records.
```SQL
-- Find top nine countries with the most cities
SELECT countries.name AS country, COUNT(*) AS cities_num
FROM countries 
LEFT JOIN cities
ON countries.code = cities.country_code
GROUP BY country
-- Order by count of cities as cities_num
ORDER BY cities_num DESC, country
LIMIT 9;
```
##### -- Write a LEFT JOIN with countries on the left and the cities on the right, joining on country code.
##### -- In the SELECT statement of your join, include country names as country, and count the cities in each country, aliased as cities_num.
##### -- Sort by cities_num (descending), and country (ascending), limiting to the first nine records.
```SQL
SELECT countries.name AS country,
-- Subquery that provides the count of cities   
  (SELECT COUNT(*) AS cities_num
   FROM cities
   WHERE countries.code = cities.country_code) AS cities_num
FROM countries
ORDER BY cities_num DESC, country
LIMIT 9;
```
