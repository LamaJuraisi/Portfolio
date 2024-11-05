# MGMT 544 - Database Management Systems

## Objective
Developed a database system to manage inventory for Fresh Harvest Grocery, aiming to track produce freshness and reduce waste.

## Tools/Techniques Used
- SQL, Google Cloud Platform (GCP) BigQuery

## Process
- Designed database schema, created tables for employees, products, suppliers, purchases, and restocking logs.
- Implemented alert thresholds for product expiration.

## Key Code Snippets
### Average Shelf Life by Product Category
```sql
SELECT P.Category, AVG(DATE_DIFF(PI.PurchaseDate, PI.ExpirationDate, DAY)) AS AvgShelfLife
FROM FreshHarvestDB.ProductInventory AS PI
JOIN FreshHarvestDB.Product AS P ON P.ProductID = PI.ProductID
GROUP BY P.Category;
```
### Employee Responsible for Restocking a Specific Product
Identifies which employee is responsible for restocking a specific product, aiding in accountability and efficient task assignment.

```sql
SELECT E.EmployeeID, E.EmployeeFirstName_, E.EmployeeLastName, 
       P.ProductID, P.Category_
FROM FreshHarvestDB.Product AS P 
JOIN FreshHarvestDB.Employee AS E ON P.Employee_ID = E.EmployeeID
WHERE P.ProductID = 1027;  -- Product number 1027 is an example of a ProductID.
```

### Minimum Alert Threshold by Product Category
Determines which product categories require closer monitoring as they near expiration, based on the lowest alert thresholds.

```sql
SELECT P.Category_, 
       MIN(PI.AlertThreshold_) AS Minimum_Alert_Threshold
FROM FreshHarvestDB.ProductInventory AS PI
JOIN FreshHarvestDB.Product AS P ON PI.ProductID = P.ProductID
GROUP BY P.Category_
ORDER BY Minimum_Alert_Threshold ASC;
```

### Supplier with Longest Shelf Life for Produce
Helps Fresh Harvest identify suppliers that deliver produce with the longest shelf life, aiding in supplier selection for freshness.

```sql
SELECT s.Supplier_Name, 
       AVG(DATE_DIFF(PI.PurchaseDate, PI.ExpirationDate, DAY)) AS Avg_Freshness_At_Arrival
FROM FreshHarvestDB.Supplier s
JOIN FreshHarvestDB.Purchase pu ON s.SupplierID = pu.SupplierID
JOIN FreshHarvestDB.Product p ON pu.ProductID = p.ProductID
JOIN FreshHarvestDB.ProductInventory PI ON pu.ProductID = PI.ProductID
WHERE p.Category_ IN ('fruits', 'vegetables', 'dairy')
GROUP BY s.Supplier_Name
ORDER BY Avg_Freshness_At_Arrival;
```

### Average Cost of Deliveries by Supplier
Calculates the average delivery cost per supplier, helping Fresh Harvest make cost-effective purchasing decisions.

```sql
SELECT s.Supplier_Name, 
       AVG(pu.Total_Cost) AS Average_Purchase_Cost
FROM FreshHarvestDB.Purchase pu
JOIN FreshHarvestDB.Supplier s ON pu.SupplierID = s.SupplierID
GROUP BY s.Supplier_Name
ORDER BY Average_Purchase_Cost ASC;
```

### Supplier with Most Cost-Effective and Freshest Produce
Identifies suppliers who offer both the longest shelf life and the lowest purchase costs for optimal inventory management.

```sql
SELECT s.Supplier_Name, 
       AVG(pu.Total_Cost) AS Average_Purchase_Cost,
       AVG(DATE_DIFF(PI.PurchaseDate, p.Expiration_Date, DAY)) AS Avg_Freshness_At_Arrival
FROM FreshHarvestDB.Supplier s
JOIN FreshHarvestDB.Purchase pu ON s.SupplierID = pu.SupplierID
JOIN FreshHarvestDB.Product p ON pu.ProductID = p.ProductID
JOIN FreshHarvestDB.ProductInventory AS PI ON p.ProductID = PI.ProductID
WHERE p.Category_ IN ('fruits', 'vegetables', 'dairy')
GROUP BY s.Supplier_Name
ORDER BY Average_Purchase_Cost ASC, Avg_Freshness_At_Arrival DESC;
```
