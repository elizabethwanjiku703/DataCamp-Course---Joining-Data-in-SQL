## Set Theories for SQL joins
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
#### **Questions**
1. Begin your query by selecting all fields from economies2015.
2. Create a second query that selects all fields from economies2019.
3. Perform a set operation to combine the two queries you just created, ensuring you do not return duplicates.

```SQL
   -- Select all fields from economies2015
   ___    
   -- Set operation
   ___
   -- Select all fields from economies2019
   ___
   -- ORDER BY code, year;
```

#### Answer
```SQL
-- Select all fields from economies2015
SELECT * 
FROM economies2015
-- Set operation
UNION
-- Select all fields from economies2019
SELECT *
FROM economies2019
ORDER BY code, year;
```
