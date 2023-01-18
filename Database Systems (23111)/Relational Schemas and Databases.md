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
```sql
CREATE PROCEDURE
```