### Introducing Inner Joins
#### -- Your First Join
###### **1. Perform an inner join with the cities table on the left and the countries table on the right; you do not need to alias tables here.**
###### **2. Join ON the country_code and code columns, making sure you identify them with the correct table.**
```SQL
SELECT *
FROM cities
INNER JOIN countries
ON cities.country_code = countries.code;
```
###### **1. Complete the SELECT statement to keep three columns: the name of the city, the name of the country, and the region the country is located in (in this order).**
###### **2. Alias the name of the city AS city and the name of the country AS country.**
```SQL
SELECT cities.name AS city, countries.name AS country, countries.region
FROM cities
INNER JOIN countries
ON cities.country_code = countries.code;
```
#### -- Joining with Alias Tables
###### **1. Start with your inner join in line 5; join the tables countries AS c (left) with economies (right), aliasing economies AS e.**
###### **2. Next, use code as your joining field in line 7; do not use the USING command here.**
###### **3. Lastly, select the following columns in order in line 2: code from the countries table (aliased as country_code), name, year, and inflation_rate.**
```SQL
SELECT c.code AS country_code, name, year, inflation_rate
FROM countries AS c
JOIN economies AS e
ON c.code = e.code;
```
#### -- USING in Action
> [!NOTE]
> When both the field names being joined on are the same then you can use USING 
###### **Use the country code field to complete the INNER JOIN with USING; do not change any alias names.**
```SQL
SELECT c.name AS country, l.name AS language, official
FROM countries AS c
INNER JOIN languages AS l
USING(code)
```
#### -- Defining Relationships
###### **1. Select the country name, aliased as country, from the countries table.**
###### **2. Now add an alias c for the countries table and perform an inner join with the languages table, l, on the right; 
join on code in line 8 with the USING keyword; include the language name, aliased as language.**
###### **3. Add a WHERE clause to find how many countries speak the language 'Bhojpuri'.**
###### **4. 
```SQL
SELECT c.name AS country, l.name AS language
FROM countries AS c
INNER JOIN languages AS l
USING(code)
WHERE l.name = 'Bhojpuri';
```
#### -- Multiple Joins
###### **1. Do an inner join of countries AS c (left) with populations AS p (right), on code. Select name and fertility_rate.**
###### **2. Chain an inner join with the economies table AS e, on code. Select year and unemployment_rate from the economies table.**
```SQL
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code;
```
###### **3. Modify your query so that you are joining to economies on year as well as code.**
```SQL
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code
-- Add an additional joining condition such that you are also joining on year
AND p.year = e.year ;
```
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
### Subqueries inside FROM
##### -- Begin with a query that groups by each country code from languages, and counts the languages spoken in each country as lang_num.
##### -- In your SELECT statement, return code and lang_num (in that order).
```SQL
SELECT l.code, COUNT(*) AS lang_num
FROM languages AS l
GROUP BY code;
```
##### -- Select local_name from countries, with the aliased lang_num from your subquery (which has been nested and aliased for you as sub).
##### -- Use WHERE to match the code field from countries and sub.
```SQL
SELECT local_name, sub.lang_num
FROM countries,
  (SELECT code, COUNT(*) AS lang_num
  FROM languages
  GROUP BY code) AS sub
-- Where codes match
WHERE countries.code = sub.code
ORDER BY lang_num DESC;
```
##### -- Select country code, inflation_rate, and unemployment_rate from economies.
##### -- Filter code for the set of countries that do not contain the words "Republic" or "Monarchy" in their gov_form.
```SQL
-- Select relevant fields
SELECT code, inflation_rate, unemployment_rate
FROM economies
WHERE year = 2015 
  AND code NOT IN
-- Subquery returning country codes filtered on gov_form
	(SELECT code
  FROM countries
  WHERE gov_form LIKE '%Monarchy%' OR gov_form LIKE '%Republic%')
ORDER BY inflation_rate;
```
## FINAL CHALLENGE
##### -- From cities, select the city name, country code, proper population, and metro area population, as well as the field city_perc, 
         which calculates the proper population as a percentage of metro area population for each city (using the formula provided).
##### -- Filter city name with a subquery that selects capital cities from countries in 'Europe' or continents with 'America' at the end of their name.
##### -- Exclude NULL values in metroarea_pop.
##### -- Order by city_perc (descending) and return only the first 10 rows.
```SQL
SELECT name, country_code, city_proper_pop, metroarea_pop, (city_proper_pop/metroarea_pop) * 100 AS city_perc
FROM cities
WHERE name IN
(
    SELECT capital
    FROM countries
    WHERE continent = 'Europe' OR continent LIKE '%America'
)
AND metroarea_pop IS NOT NULL
ORDER BY city_perc DESC
LIMIT 10;
```


