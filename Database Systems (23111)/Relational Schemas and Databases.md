## Assorted SQL Snippets
#### Filter by an Aggregate
given the table `SoldProducts` which has a list of products and individual prices for which they were sold, find all products that have sold more than £100 and that aren't in the "Technology" category.
```sql
SELECT name, category, SUM(price) FROM SoldProducts
WHERE category != "Technology" -- Filter by non-aggregates here
GROUP BY name
HAVING SUM(price) > 100; -- Filter by aggregates here
```
#### Stored Procedures
Simple example:
```sql
DELIMITER //    -- Change delimiter so that ; can be used in the procedure

CREATE PROCEDURE GetAllProducts()    -- Create the procedure
BEGIN
	SELECT * FROM Products;
END //    -- Using changed delimiter

DELIMITER ;    -- Change delimiter back to normal

-- later on...
CALL GetAllProducts();
```
Local and output variables:
```
```