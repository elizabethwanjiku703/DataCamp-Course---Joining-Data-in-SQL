## Joining-Data-in-SQL
--- UNION Vs UNION ALL
SELECT * 
FROM languages
UNION
SELECT * 
FROM currencies;
##### in this type of code an error can occur if both tables do not have the same number of fields.

