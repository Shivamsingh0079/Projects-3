# Projects-3
-- ============================================================
-- DECODELABS DATA ANALYTICS — PROJECT 3: SQL DATA ANALYSIS
-- Dataset: orders (1,200 records, cleaned in Project 1)
-- Analyst: Intern, Data Analytics Track | Batch 2026
-- ============================================================

-- ------------------------------------------------------------
-- Q1. Basic SELECT — inspect the structure of the table
-- ------------------------------------------------------------
SELECT OrderID, Date, Product, Quantity, UnitPrice, TotalPrice, OrderStatus
FROM orders
LIMIT 5;


-- ------------------------------------------------------------
-- Q2. WHERE — comparison filter
-- Find all high-value orders (TotalPrice > $2,500)
-- ------------------------------------------------------------
SELECT OrderID, Product, Quantity, UnitPrice, TotalPrice
FROM orders
WHERE TotalPrice > 2500
ORDER BY TotalPrice DESC;


-- ------------------------------------------------------------
-- Q3. WHERE — pattern matching with LIKE
-- Find all orders shipped to addresses on "Main St"
-- ------------------------------------------------------------
SELECT OrderID, ShippingAddress, Product, TotalPrice
FROM orders
WHERE ShippingAddress LIKE '%Main St%'
LIMIT 10;


-- ------------------------------------------------------------
-- Q4. WHERE — multiple conditions (AND / OR)
-- Find Cancelled or Returned orders paid by Credit Card
-- ------------------------------------------------------------
SELECT OrderID, PaymentMethod, OrderStatus, TotalPrice
FROM orders
WHERE (OrderStatus = 'Cancelled' OR OrderStatus = 'Returned')
  AND PaymentMethod = 'Credit Card'
ORDER BY TotalPrice DESC;


-- ------------------------------------------------------------
-- Q5. ORDER BY — top 10 highest-value orders
-- ------------------------------------------------------------
SELECT OrderID, CustomerID, Product, TotalPrice
FROM orders
ORDER BY TotalPrice DESC
LIMIT 10;


-- ------------------------------------------------------------
-- Q6. GROUP BY + COUNT — order volume per product
-- ------------------------------------------------------------
SELECT Product, COUNT(*) AS OrderCount
FROM orders
GROUP BY Product
ORDER BY OrderCount DESC;


-- ------------------------------------------------------------
-- Q7. GROUP BY + SUM + AVG — revenue performance per product
-- ------------------------------------------------------------
SELECT
    Product,
    COUNT(*)            AS OrderCount,
    SUM(TotalPrice)      AS TotalRevenue,
    ROUND(AVG(TotalPrice), 2) AS AvgOrderValue
FROM orders
GROUP BY Product
ORDER BY TotalRevenue DESC;


-- ------------------------------------------------------------
-- Q8. GROUP BY + HAVING — filter aggregated groups
-- Payment methods with average order value above $1,050
-- ------------------------------------------------------------
SELECT
    PaymentMethod,
    COUNT(*)                  AS OrderCount,
    ROUND(AVG(TotalPrice), 2) AS AvgOrderValue
FROM orders
GROUP BY PaymentMethod
HAVING AVG(TotalPrice) > 1050
ORDER BY AvgOrderValue DESC;


-- ------------------------------------------------------------
-- Q9. GROUP BY on OrderStatus — the "cancel/return risk" query
-- ------------------------------------------------------------
SELECT
    OrderStatus,
    COUNT(*)                           AS OrderCount,
    SUM(TotalPrice)                    AS Revenue,
    ROUND(100.0 * COUNT(*) / (SELECT COUNT(*) FROM orders), 1) AS PctOfOrders
FROM orders
GROUP BY OrderStatus
ORDER BY OrderCount DESC;


-- ------------------------------------------------------------
-- Q10. Multi-column GROUP BY — product performance by payment method
-- ------------------------------------------------------------
SELECT
    Product,
    PaymentMethod,
    COUNT(*)            AS OrderCount,
    SUM(TotalPrice)      AS Revenue
FROM orders
GROUP BY Product, PaymentMethod
ORDER BY Product, Revenue DESC;


-- ------------------------------------------------------------
-- Q11. Subquery — orders priced above the overall average
-- ------------------------------------------------------------
SELECT OrderID, Product, TotalPrice
FROM orders
WHERE TotalPrice > (SELECT AVG(TotalPrice) FROM orders)
ORDER BY TotalPrice DESC
LIMIT 10;


-- ------------------------------------------------------------
-- Q12. CASE WHEN — bucket orders into value tiers, then aggregate
-- ------------------------------------------------------------
SELECT
    CASE
        WHEN TotalPrice < 500  THEN '1. Under $500'
        WHEN TotalPrice < 1000 THEN '2. $500 - $999'
        WHEN TotalPrice < 2000 THEN '3. $1,000 - $1,999'
        ELSE '4. $2,000+'
    END AS ValueTier,
    COUNT(*)             AS OrderCount,
    SUM(TotalPrice)       AS Revenue
FROM orders
GROUP BY ValueTier
ORDER BY ValueTier;


-- ------------------------------------------------------------
-- Q13. Referral source performance — marketing channel ROI signal
-- ------------------------------------------------------------
SELECT
    ReferralSource,
    COUNT(*)                  AS OrderCount,
    ROUND(AVG(TotalPrice), 2) AS AvgOrderValue,
    SUM(TotalPrice)            AS TotalRevenue
FROM orders
GROUP BY ReferralSource
ORDER BY TotalRevenue DESC;


-- ------------------------------------------------------------
-- Q14. Coupon impact — does a coupon change basket size?
-- ------------------------------------------------------------
SELECT
    CouponCode,
    COUNT(*)                     AS OrderCount,
    ROUND(AVG(ItemsInCart), 2)   AS AvgItemsInCart,
    ROUND(AVG(TotalPrice), 2)    AS AvgOrderValue
FROM orders
GROUP BY CouponCode
ORDER BY AvgOrderValue DESC;


-- ------------------------------------------------------------
-- Q15. Top 5 customers by lifetime spend (repeat customer check)
-- ------------------------------------------------------------
SELECT
    CustomerID,
    COUNT(*)          AS NumberOfOrders,
    SUM(TotalPrice)    AS LifetimeSpend
FROM orders
GROUP BY CustomerID
HAVING COUNT(*) > 1
ORDER BY LifetimeSpend DESC
LIMIT 5;
