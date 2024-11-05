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

