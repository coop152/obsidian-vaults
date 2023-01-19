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
Parameters:
```sql
DELIMITER //

CREATE PROCEDURE GetOrdersByDate(
	IN orderDate DATE,
	OUT orderCount INT)
BEGIN
	SELECT * FROM Orders
	WHERE Orders.date = orderDate;

	SELECT COUNT(*) INTO orderCount FROM Orders
	WHERE Orders.date = orderDate;
END

DELIMITER ;
```
List all stored procedures:
```sql
SELECT routine_name FROM information_schema.routines
WHERE routine_type = "PROCEDURE" AND routine_schema = "classicmodels";
```
Control Flow, local variables:
```sql
DELIMITER //

CREATE PROCEDURE GetCustomerLevel(
IN pCustomerNumber INT,
OUT pCustomerLevel VARCHAR(20))
BEGIN
	DECLARE credit DECIMAL(10,2) DEFAULT 0;

	SELECT creditLimit INTO credit FROM customers
		WHERE customerNumber = pCustomerNumber;

	IF credit <= 10000 THEN
		SET pCustomerLevel = 'SILVER';
	ELSEIF credit <= 50000 THEN
		SET pCustomerLevel = 'GOLD';
	ELSEIF credit > 50000 THEN
		SET pCustomerLevel = 'PLATINUM';
	END IF;
END //

DELIMITER ;
```
Case statement:
```sql
CASE country
	WHEN 'USA' THEN
		SET pShipping = '2-day shipping';
	WHEN 'Canada' THEN
		SET pShipping = '3-day shipping';
	ELSE
		SET pShipping = '5-day shipping';
END CASE;
```
Single-line trigger:
```sql
CREATE TRIGGER BEFORE DELETE
ON Customers
FOR EACH ROW
	INSERT INTO CustomerAudit(deletedCustomerID, deletionTime)
	VALUES (OLD.customerID, NOW());
```
Multi-line trigger:
```sql
DELIMITER //

CREATE TRIGGER AFTER INSERT
ON Orders
FOR EACH ROW
BEGIN
	DECLARE 
END //

DELIMITER ;
```