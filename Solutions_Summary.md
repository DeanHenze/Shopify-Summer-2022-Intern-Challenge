# Summer 2022 Data Science Intern Challenge

## Summary of Solutions

### Question 1: 

Given some sample data, write a program to answer the following:

On Shopify, we have exactly 100 sneaker shops, and each of these shops sells only one model of shoe. We want to do some analysis of the average order value (AOV). When we look at orders data over a 30 day window, we naively calculate an AOV of $3145.13. Given that we know these shops are selling sneakers, a relatively affordable item, something seems wrong with our analysis. 

a.	Think about what could be going wrong with our calculation. Think about a better way to evaluate this data. 

**The AOV calculation is being skewed by a small number of very large order amounts (they make up <1.5% of all orders).**

b.	What metric would you report for this dataset?

**The quick and easy solution is to use the median order value (MOV). It is not affected by a few outliers, and by inspection we see that the distribution of order amounts are fairly clustered around the MOV.**

**A slightly longer solution is to find that the orders can be grouped into three categories (see supplementary .pynb file):**

**group 1) Lower number of units sold, lower cost per sneaker.**

**group 2) Lower number of units sold, higher cost per sneaker.**

**group 3) Higher number of units sold, lower cost per sneaker.**

**Each of these groups has a distinct MOV, and for each group the MOV and AOV agree well.**

c.	What is its value?

**Quick and easy solution: MOV = $284**

**Longer solution:**

**group 1) MOV = $284**

**group 2) MOV = $51,450**

**group 3) MOV = $704,000**


### Question 2: 
For this question you’ll need to use SQL. Please use queries to answer the following questions. Paste your queries along with your final numerical answers below.

**My solutions use SQL Server/MS Access syntax.**

a.	How many orders were shipped by Speedy Express in total?

**Result: 54**

**Query:**
```
SELECT COUNT(*)
FROM Orders
INNER JOIN Shippers
ON Orders.ShipperID = Shippers.ShipperID
WHERE Shippers.ShipperName = 'Speedy Express';
```

b.	What is the last name of the employee with the most orders?

**Result: Peacock**

**Query:**
```
SELECT LastName
FROM Employees
WHERE EmployeeID = (
	SELECT TOP 1 EmployeeID
	FROM Orders
	GROUP BY EmployeeID
	ORDER BY COUNT(*) DESC;
	);
```

c.	What product was ordered the most by customers in Germany?

**I was unsure if the question referred to highest quantity bought or most orders placed. I chose to answer the former, although determining the latter can be done by replacing the SUM aggregate with COUNT on the ORDER BY statement (last row).**

**Result: Boston Crab Meat**

**Columns needed from specific tables:**
**- Country, CustomerID columns in the ‘Customers’ table.**
**- CustomerID, OrderID columns in the ‘Orders’ table.**
**- OrderID, ProductID columns in the ‘OrderDetails’ table.**
**- ProductID, ProductName columns in the ‘Products’ table.**

Query:
SELECT TOP 1 Products.ProductName
FROM (((Customers
INNER JOIN Orders ON Customers.CustomerID = Orders.CustomerID)
INNER JOIN OrderDetails ON Orders.OrderID = OrderDetails.OrderID)
INNER JOIN Products ON OrderDetails.ProductID = Products.ProductID
)
WHERE Customers.Country = 'Germany'
GROUP BY Products.ProductName
ORDER BY SUM(OrderDetails.Quantity) DESC;
