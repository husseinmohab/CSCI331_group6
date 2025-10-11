## Links to videos:

MdIsmailHossain [Presentation video link here](https://youtu.be/zN9dYV35IOc) (unfourtunately this is an individual video due to a confilict in schedule)

Ilya Yahoshchankau & Ranjan Bhattarai [Presentation video link here](https://youtu.be/qum5RiKtx3A)

Mohab Hussein & Gabriel Zamora [Presentation video link here](https://youtu.be/PMcu8QIeFvI)

## Project Plan:

# Project Plan [ HW 4&5 ]

Through group collaboration we have formulated 50 propositions, debated on their quality, guided each other on how to tackle them through functional specifications, and finally had them assigned to us (by our designated leader Ranjan) so we could implement queries to satisfy our portion of propositions.

---

### TODO (Mohab):

1.  **Identify legacy customers whose initial order date is older than the average initial order date across the customer base.**
    * **Functional Specification**
        * Join `Sales.Customers` (c) to `Sales.Orders` (o).
        * Find the first order date for each customer using `MIN(o.OrderDate)` and `GROUP BY`.
        * Use a nested subquery to calculate the average of all first order dates.
        * Filter the results in the `HAVING` clause to keep only customers whose first order date is earlier than the calculated average.

2.  **Perform a market analysis to identify products frequently purchased along with the 'USB missile launcher (Update)'.**
    * **Functional Specification**
        * Use a subquery to find the `StockItemID` for 'USB missile launcher (Update)'.
        * Use another subquery with the `IN` predicate to get all `OrderID`s that contain this item.
        * Select all distinct `StockItemName` from those orders.
        * Exclude the 'USB missile launcher (Update)' itself from the final results.

3.  **Identify salespeople who have successfully sold to every existing customer category.**
    * **Functional Specification**
        * Join `People` to `Orders` and `Customers` to link salespeople to customer categories.
        * Group the results by salesperson (`p.FullName`).
        * Use a subquery in the `HAVING` clause to get the total count of all customer categories.
        * Compare the count of distinct categories for each salesperson to the total count; only return those who match.

4.  **Isolate suppliers who exclusively provide ambient (non-chiller) stock items.**
    * **Functional Specification**
        * Use a subquery with the `NOT IN` predicate.
        * The subquery creates a list of all distinct `SupplierID`s that are linked to a chiller stock item (`IsChillerStock = 1`).
        * The outer query selects all suppliers from `Purchasing.Suppliers` whose `SupplierID` is not in this list.

5.  **List products priced above average and show their price difference.**
    * **Functional Specification**
        * Use a scalar subquery to calculate the single average `UnitPrice` from `Warehouse.StockItems`.
        * Filter the `StockItems` table to select rows where `UnitPrice` is greater than this average.
        * In the `SELECT` list, calculate the difference between the item's `UnitPrice` and the average price.
        * Order the results to show the most expensive items first.

6.  **Determine the top performing salesperson by total revenue for each fiscal year.**
    * **Functional Specification**
        * Create a CTE named `SalesRanks`.
        * Inside the CTE, join `Invoices`, `InvoiceLines`, and `People`.
        * Group by year and salesperson to calculate total revenue.
        * Use the `ROW_NUMBER()` window function to rank salespeople within each year by their revenue.
        * Select from the CTE where the rank is 1.

7.  **Calculate the customer lifecycle duration by identifying the first and last order dates for each customer.**
    * **Functional Specification**
        * Create a derived table aliased as 'cod'.
        * Inside the derived table, group the `Sales.Orders` table by `CustomerID`.
        * Use `MIN(OrderDate)` and `MAX(OrderDate)` to find the first and last order dates for each customer.
        * Join the main `Customers` table to this derived table on `CustomerID` to link the dates to customer names.

8.  **Identify the top 3 revenue-generating products within each customer category.**
    * **Functional Specification**
        * Use `CROSS APPLY` to run a correlated subquery for each row in `Sales.CustomerCategories`.
        * The subquery joins `StockItems`, `OrderLines`, `Orders`, and `Customers`.
        * It filters for sales belonging to the current customer category.
        * It groups by `StockItemName` to calculate total revenue per product and selects the `TOP 3`.
        * The final result combines each category with its top 3 products.

9.  **Segment the customer base into value tiers ('High', 'Medium', 'Low') based on total historical spending.**
    * **Functional Specification**
        * Create a CTE named `CustomerSpending`.
        * Inside the CTE, join `Invoices` and `InvoiceLines` and group by `CustomerID` to calculate total spending for each customer.
        * Join the main `Customers` table to the CTE.
        * Use a `CASE` expression in the `SELECT` list to assign a `CustomerTier` based on the total spending amount.

10. **For each invoice, provide a breakdown of the total value of 'Chiller' vs. 'Ambient' items.**
    * **Functional Specification**
        * Create a CTE named `InvoiceComposition`.
        * Inside the CTE, join `InvoiceLines` and `StockItems`.
        * Use a `CASE` expression on `si.IsChillerStock` to create a new column, `ItemCategory` ('Chiller' or 'Ambient').
        * Group by `InvoiceID` and the new `ItemCategory` to sum the value for each type within each invoice.
        * The final query selects from the CTE to display the categorized breakdown.

---

### TODO (Gabriel):

1.  **Find the latest order date in the `Sales.Orders` table.**
    * **Functional Specification**
        * Use a subquery to find the `MAX(OrderDate)` from `Sales.Orders`.
        * Select all orders from `Sales.Orders` where `OrderDate` equals that maximum date.
        * Display `OrderID`, `CustomerID`, and `OrderDate`.
        * This identifies all orders made on the company’s latest active day.

2.  **Identify the customer who placed the highest number of orders.**
    * **Functional Specification**
        * Use a subquery with `GROUP BY` and `COUNT(*)` to find which customer placed the most orders.
        * Apply `TOP (1) WITH TIES` to include any customers tied for the top count.
        * Join the result back to `Sales.Orders` to display all order details for that customer.
        * Show `CustomerID`, `OrderID`, and `OrderDate`.

3.  **Find employees who did not place any orders after May 2022.**
    * **Functional Specification**
        * Select all employees from `Application.People`.
        * Use a `NOT IN` subquery on `Sales.Orders` to find employees whose IDs don’t appear in orders after '2022-05-01'.
        * Display each employee’s `PersonID` and `FullName`.
        * Optionally include their last order date using a subquery for reference.

4.  **List all orders placed on each customer’s last active day.**
    * **Functional Specification**
        * Use a correlated subquery to find the last order date for each customer from `Sales.Orders`.
        * Match the outer query’s `CustomerID` with the subquery’s `CustomerID`.
        * Return only the orders where the date equals the customer’s most recent order date.
        * Display `CustomerID`, `OrderID`, and `OrderDate`.

5.  **Find stock items that were not ordered during the year 2015.**
    * **Functional Specification**
        * Select `StockItemID` and `StockItemName` from `Warehouse.StockItems`.
        * Use a `NOT EXISTS` subquery to find items that were never ordered in 2015.
        * Join `Sales.OrderLines` and `Sales.Orders` inside the subquery to match `OrderDate` and `StockItemID`.
        * Filter `OrderDate` between '2015-01-01' and '2016-01-01'.
        * Return only the stock items that did not appear in any 2015 orders.

6.  **List all orders that were not placed on the last day of their respective year.**
    * **Functional Specification**
        * Create a CTE that adds a column for the end-of-year date using `DATEFROMPARTS`.
        * Compare each order’s `OrderDate` to the calculated year-end date.
        * Filter out any orders placed exactly on the last day of that year.
        * Display `OrderID`, `OrderDate`, and `CustomerID`.

7.  **Find the most recent order per employee.**
    * **Functional Specification**
        * Join `Sales.Orders` to `Application.People` on `SalespersonPersonID` = `PersonID`.
        * Use a subquery that finds the `MAX(OrderDate)` for each `SalespersonPersonID` in `Sales.Orders`.
        * Show `SalespersonPersonID`, `FullName`, `OrderID`, and `OrderDate`.

8.  **Q2-2015 orders per salesperson (using a CTE)**
    * **Functional Specification**
        * Define a CTE named `OrdersQ2_2015` that filters `Sales.Orders` to include only orders placed between ‘2015-04-01’ and ‘2015-07-01’.
        * From the CTE, select `SalespersonPersonID` and `OrderID` to prepare data for aggregation.
        * Join the CTE to `Application.People` on `SalespersonPersonID` = `PersonID` to display each salesperson’s name.
        * Use `COUNT` to calculate how many orders each salesperson handled during Q2 of 2015.

9.  **Order totals via a derived table**
    * **Functional Specification**
        * Create a derived table (`OrderTotals`) that sums each order’s total by multiplying `Quantity * UnitPrice` in `Sales.OrderLines`.
        * Group the derived table by `OrderID` to calculate one total per order.

10. **List orders and their total quantity using a derived table**
    * **Functional Specification**
        * Build a derived table (`t`) that calculates each order’s total quantity by summing the `Quantity` column from `Sales.OrderLines`.
        * Group the derived table by `OrderID` to get one total quantity per order.

---

### TODO (Ranjan):

1.  **Finding customers who have placed orders with a total value greater than the average order value**
    * **Functional Specification:**
        * Main query: Join `Sales.Customers` (c) → `Sales.Orders` (o) → `Sales.OrderLines` (ol)
        * Compute total order value per customer as `SUM(ol.Quantity * ol.UnitPrice)`
        * Subquery in `HAVING`: Calculate average order value across all orders
        * Subquery logic: Sum line items per `OrderID`, then calculate `AVG` of those totals
        * Group by `CustomerID` and `CustomerName` to aggregate all orders per customer
        * Filter customers whose total order value exceeds the average order value
        * Sort by `TotalOrderValue` in descending order
        * Result shows customers who spend more than the average order amount

2.  **Finding suppliers who supply products that are currently almost running out of stock, with less than 100 items**
    * **Functional Specification:**
        * Main query: `SELECT` from `Purchasing.Suppliers` (s)
        * Subquery: Find `SupplierID`s that have products with low stock
        * Subquery joins `Warehouse.StockItems` (si) → `Warehouse.StockItemHoldings` (sih)
        * Filter products where `QuantityOnHand < 100`
        * Main query filters suppliers using `IN` clause with subquery results
        * Use `DISTINCT` to avoid duplicate suppliers if they have multiple low-stock products
        * Sort by `SupplierName` alphabetically
        * Result shows supplier details (ID, name, phone) for suppliers with low-stock items

3.  **List employees who have processed more orders than the average employee**
    * **Functional Specification:**
        * Main query: Join `Application.People` (p) → `Sales.Orders` (o) on `PickedByPersonID`
        * Count orders processed per employee using `COUNT(o.OrderID)`
        * Subquery 1 (in `SELECT`): Calculate average orders per employee across all employees
        * Subquery 2 (in `HAVING`): Same calculation to filter employees above average
        * Subquery logic: Count orders per employee, then calculate `AVG` of those counts
        * Group by `PersonID` and `FullName` to aggregate orders per employee
        * Filter using `HAVING` to show only employees above average
        * Sort by `OrdersProcessed` in descending order
        * Result shows employee details, their order count, and the average for comparison

4.  **Yearly Sales Performance Summary**
    * **Functional Specification:**
        * `YearlySales` aggregates sales metrics by year
        * Join `Sales.Orders` (o) → `Sales.OrderLines` (ol)
        * Extract year using `YEAR(o.OrderDate)`
        * Calculate yearly total as `SUM(ol.Quantity * ol.UnitPrice)`
        * Count distinct orders using `COUNT(DISTINCT o.OrderID)`
        * Group by `OrderYear`
        * Main query computes `AvgOrderValue = YearlyTotal / TotalOrders`
        * Round `YearlyTotal` and `AvgOrderValue` to 2 decimal places
        * Sort by `OrderYear` chronologically

5.  **Products with Total Quantity Sold**
    * **Functional Specification:**
        * `ProductSales` aggregates quantity sold per product
        * Join `Warehouse.StockItems` (si) → `Sales.OrderLines` (ol)
        * Calculate total quantity as `SUM(ol.Quantity)`
        * Group by `StockItemID` and `StockItemName`
        * Main query filters products with `TotalQuantitySold > 100`
        * Sort by `TotalQuantitySold` in descending order

6.  **Monthly Order Count Summary**
    * **Functional Specification:**
        * `MonthlyOrders` counts orders per month
        * Extract year using `YEAR(OrderDate)` and month using `MONTH(OrderDate)`
        * Count total orders using `COUNT(*)`
        * Group by `OrderYear` and `OrderMonth`
        * Main query returns all monthly order counts
        * Sort by `OrderYear` and `OrderMonth` chronologically

7.  **High-Volume Sales Employees**
    * **Functional Specification:**
        * `EmployeeOrders` counts orders handled by each employee
        * Join `Application.People` (p) → `Sales.Orders` (o) on `SalespersonPersonID`
        * Filter only employees using `WHERE p.IsEmployee = 1`
        * Count orders using `COUNT(o.OrderID)`
        * Group by `PersonID` and `FullName`
        * Main query filters employees with `TotalOrders > 50`
        * Sort by `TotalOrders` in descending order

8.  **Average Order Value by Customer**
    * **Functional Specification:**
        * `CustomerOrderValues` calculates order metrics per customer
        * Join `Sales.Customers` (c) → `Sales.Orders` (o) → `Sales.OrderLines` (ol)
        * Count distinct orders using `COUNT(DISTINCT o.OrderID)`
        * Calculate total spending as `SUM(ol.Quantity * ol.UnitPrice)`
        * Group by `CustomerID` and `CustomerName`
        * Main query computes `AvgOrderValue = TotalSpent / OrderCount`
        * Round `AvgOrderValue` to 2 decimal places
        * Sort by `AvgOrderValue` in descending order

9.  **Top 5 Products by Revenue**
    * **Functional Specification:**
        * `ProductRevenue` calculates total revenue per product
        * Join `Warehouse.StockItems` (si) → `Sales.OrderLines` (ol)
        * Calculate revenue as `SUM(ol.Quantity * ol.UnitPrice)`
        * Group by `StockItemName`
        * Main query selects `TOP 5` products by revenue
        * Round `TotalRevenue` to 2 decimal places
        * Sort by `TotalRevenue` in descending order

10. **Inactive Customers (No Orders in 6 Months)**
    * **Functional Specification:**
        * `LastOrderDate` finds most recent order per customer
        * Join `Sales.Customers` (c) → `Sales.Orders` (o)
        * Find last order date using `MAX(o.OrderDate)`
        * Calculate days since last order using `DATEDIFF(DAY, MAX(o.OrderDate), GETDATE())`
        * Group by `CustomerID` and `CustomerName`
        * Main query filters customers with `DaysSinceLastOrder > 180`
        * Sort by `DaysSinceLastOrder` in descending order

---

### TODO (Ilya):

1.  **List Customers With Sales Above the Average Customer Sales**
    * **Functional Specification:**
        * Sum sales per customer for `Sales.Orders` → `Sales.OrderLines`.
        * Compare each customer’s total to average total per customer (subquery).
        * Return customers whose total > global average; order by total desc.

2.  **List Customers Who Has At Least 1 Order That Contain at Least 3 Distinct Items**
    * **Functional Specification:**
        * For each order, check the amount of distinct positions by subquery(`Sales.OrderLines`).
        * Return Customers With `DISTINCT StockItemID >= 3`.

3.  **List Top-3 Suppliers by Number of Items**
    * **Functional Specification:**
        * Count The Number Of Orders For each Supplier(`Purchasing.PurchaseOrders`).
        * Join With `Purchasing.Suppliers` For Names.

4.  **Show Last Order per Customer (Top 20 customers)**
    * **Functional Specification:**
        * For each customer, find the `MAX(OrderDate)` using a correlated subquery for `Sales.Orders`.
        * Return the customer ID, order ID and the date of their most recent order.
        * Sort by date.

5.  **Show Customers Who Spent More Than The Average Customer**
    * **Functional Specification:**
        * Calculate global average spending per customer (declare a variable).
        * Sum total sales per customer using `Sales.Customers`, `Sales.Orders`, `Sales.OrderLines`.
        * Return customers whose spending > average; order by total descending.

6.  **Count the number of orders yearly**
    * **Functional Specification:**
        * Extract `YEAR(OrderDate)` and `CustomerID` from `Sales.Orders`.
        * Group by year and count distinct customers.

7.  **Show Customers With Total Sales per Year**
    * **Functional Specification:**
        * Create derived table with yearly customer totals from `Sales.Orders` and `Sales.OrderLines`.
        * Join to `Sales.Customers` to show their yearly sales.

8.  **List Customers With Invoices Count**
    * **Functional Specification:**
        * Create derived table with count of invoices per customer from `Sales.Invoices`.
        * Return all customers with that count from `Sales.Customers`.

9.  **Show Top 3 Suppliers by Orders From Purchasing**
    * **Functional Specification:**
        * Create derived table with count of purchase orders per supplier from `Purchasing.PurchaseOrders`.
        * Select top 3 suppliers by total orders from `Purchasing.Suppliers`.

10. **List Stock Value by Product At Warehouse**
    * **Functional Specification:**
        * Calculate stock value per item = `Quantity * UnitCost` from `Warehouse.StockItemHoldings`.
        * Return top 10 by total value with `Warehouse.StockItems`.

---

### TODO (Ismail):

1.  **List of Products that cost more than the average**
    * **Functional Specification**
        * Use a scalar subquery to calculate the average `UnitPrice` from the `Warehouse.StockItems` table.
        * Select the `StockItemID`, `StockItemName`, and `UnitPrice` from the `Warehouse.StockItems` table.
        * Filter the results in the `WHERE` clause to only include products whose `UnitPrice` is greater than the calculated average.
        * Limit the output to the `TOP 7` results.

2.  **Most recent 5 orders between 2015–2016**
    * **Functional Specification**
        * Create a derived table aliased as 'o'.
        * Inside the derived table, select `OrderID`, `CustomerID`, and `OrderDate` from the `Sales.Orders` table.
        * Filter the records within the derived table to include only orders placed between January 1, 2015, and December 31, 2016.
        * Select the specified columns from the derived table.
        * Order the final results by `OrderDate` in descending order to show the newest orders first.
        * Limit the output to the `TOP 5` most recent orders.

3.  **Products starting with 'A' (first 7 by StockItemID)**
    * **Functional Specification**
        * Select `StockItemID`, `StockItemName`, and `SupplierID` from the `Warehouse.StockItems` table (aliased as 's').
        * Use a correlated scalar subquery in the `SELECT` list to count the total number of items from the same supplier (`s2.SupplierID = s.SupplierID`) whose names also start with 'A'.
        * Filter the main query in the `WHERE` clause to only include products whose `StockItemName` starts with the letter 'A'.
        * Order the results by `StockItemID` in ascending order.
        * Limit the output to the `TOP 7` results.

4.  **Return all products whose unit price is greater than at least one product’s unit price from Stock Group 3.**
    * **Functional Specification**
        * Use a subquery with the `ANY` predicate to generate a list of unit prices.
        * Inside the subquery, join `Warehouse.StockItems` and `Warehouse.StockItemStockGroups` to filter for products where the `StockGroupID` is 3.
        * The outer query selects `StockItemID`, `StockItemName`, and `UnitPrice` from `Warehouse.StockItems`.
        * Filter the results in the `WHERE` clause to include any product whose `UnitPrice` is greater than at least one of the prices from the subquery's list.
        * Limit the output to the `TOP 8` results.

5.  **Most recent order date for each customer**
    * **Functional Specification**
        * Select `CustomerID` and `CustomerName` from the `Sales.Customers` table (aliased as 'C').
        * Use a correlated scalar subquery in the `SELECT` list to find the latest order date for each customer.
        * The subquery calculates the `MAX(OrderDate)` from the `Sales.Orders` table for the corresponding `CustomerID` from the outer query (`O.CustomerID = C.CustomerID`).
        * Limit the output to the `TOP 7` customers.

6.  **Top 7 selling products by total quantity sold**
    * **Functional Specification**
        * Create a derived table aliased as 'dt' to pre-calculate sales totals.
        * Inside the derived table, group the `Sales.OrderLines` table by `StockItemID` and use `SUM(Quantity)` to find the total quantity sold for each item.
        * Join the derived table to the `Warehouse.StockItems` table on `StockItemID` to retrieve the `StockItemName`.
        * Order the final results by the `TotalSold` in descending order to rank products from most to least sold.
        * Limit the output to the `TOP 7` selling products.

7.  **5 most recent orders**
    * **Functional Specification**
        * Create a Common Table Expression (CTE) named `OrderedCTE`.
        * Inside the CTE, select order details from the `Sales.Orders` table.
        * Use the `ROW_NUMBER()` window function ordered by `OrderDate` descending to assign a sequential rank to each order, starting with 1 for the most recent.
        * Select `OrderID`, `CustomerID`, and `OrderDate` from the CTE.
        * Filter the results in the `WHERE` clause to keep only rows where the rank (`RowNum`) is less than or equal to 5.
        * Order the final result by `OrderDate` descending.

8.  **Total order amount per customer (top 5)**
    * **Functional Specification**
        * Declare an integer variable `@TopN` and set its value to 5.
        * Call the table-valued function `Sales.fn_TopCustomerTotals`, passing the `@TopN` variable as a parameter.
        * Join the results of the function (aliased as 't') with the `Sales.Customers` table on `CustomerID` to retrieve customer names.
        * Order the final result by `TotalAmount` in descending order to show the highest spenders first.

9.  **Customers spending more than $50,000**
    * **Functional Specification**
        * Create a CTE named `CustomerTotals`.
        * Inside the CTE, join `Sales.Orders` and `Sales.OrderLines`.
        * Group the results by `CustomerID` to calculate the `SUM` of `Quantity * UnitPrice` (as `TotalSpent`) and the `MAX(OrderDate)` (as `MostRecentOrder`) for each customer.
        * Join the `CustomerTotals` CTE with the `Sales.Customers` table to get customer names.
        * Filter the results to only include customers where `TotalSpent` is greater than 50,000.
        * Order the results by `MostRecentOrder` in descending order.
        * Limit the output to the `TOP 7` results.

10. **Categorize products by price**
    * **Functional Specification**
        * Create a CTE named `ProductCategories`.
        * Inside the CTE, select product information from the `Warehouse.StockItems` table.
        * Filter the records to only include a specific list of `StockItemID`s (1, 3, 8, 177, 206).
        * Use a `CASE` expression to create a new column `PriceCategory` based on the `UnitPrice`: 'Cheap' (< 10), 'Moderate' (10-100), or 'Expensive' (> 100).
        * The final query selects all columns from the CTE.
        * Order the results by `StockItemID`.
