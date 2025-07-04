# Kultra Mega Stores – Inventory & Sales Analysis (2009–2012)

This repository contains the complete SQL-based business intelligence case study for **Kultra Mega Stores (KMS)** focused on the **Abuja division** between **2009 and 2012**. The analysis was conducted using SQL Server Management Studio (SSMS), Excel, and Power BI, and the results inform sales strategy, customer behavior, and shipping logistics.


## 📌 Table of Contents

1. [Project Overview](#1-project-overview)  
2. [Data Source](#2-data-source)  
3. [Tools and Technique](#3-tools-and-technique)  
4. [Data Processing and Cleaning](#4-data-processing-and-cleaning)  
5. [Exploratory Data Analysis](#5-exploratory-data-analysis)  
6. [Results](#6-results)  
7. [Observations and Insights](#7-observations-and-insights)  
8. [Recommendations](#8-recommendations)  
9. [Limitations](#9-limitations)
10. [Sample SQL Queries Used](#10-Sample-SQL-Queries-Used)


## 1. Project Overview

Kultra Mega Stores (KMS) specializes in office supplies and furniture. This case study focuses on analyzing 4 years' worth of transactional order data for the **Abuja division** to derive actionable insights for sales, customer segmentation, product category performance, and shipping logistics.


## 2. Data Source

- `KMS_Orders_2009_2012.xlsx`: Contains 4 years of customer orders.
- `order_status`: Contains order return status and shipping information.

## 3. Tools and Technique

- **SQL Server Management Studio (SSMS)** – Querying and aggregations  
- **Microsoft Excel** – Result exports and formatting  

## 4. Data Processing and Cleaning

- Removed nulls and duplicates in `Order_ID`, `Sales`, `Product_Category`.
- Standardized `Customer_Segment` and `Shipping_Mode` values.
- Joined `KMS` and `order_status` tables using `Order_ID` for full analysis.


## 5. Exploratory Data Analysis

Performed via SQL aggregations and grouped metrics:

- Total sales by category, region, and customer
- Profitability by customer segment
- Shipping cost evaluation by mode and priority
- Product category breakdown for top customers
- Returned orders by customer


## 6. Results

| Metric | Result |
|--------|--------|
| **Top product category** | Technology (₦5,884,248.10) |
| **Top 3 regions by sales** | West, Ontario, Prairie |
| **Appliance sales in Ontario** | ₦202,548.84 |
| **Shipping method with highest cost** | Delivery Truck (₦51,911.94) |
| **Top 5 customers by sales** | Emily Phan, Deborah Brumfield, Roy Skaria, Sylvia Foulston, Grant Carroll |
| **Top small business customer** | Dennis Krink |
| **Top corporate customer by orders** | Adam Hunt (27 orders) |
| **Most profitable consumer customer** | Emily Phan (₦34,005.44) |
| **Returned order customers** | 15 distinct customers from various segments |
| **Shipping vs Order Priority Summary** | Mismatch observed between priority level and shipping cost |


## 7. Observations and Insights

- **Product Performance**: Technology leads all categories in total sales, which suggests a strong market demand for electronic products in Abuja.

- **Regional Sales**: Western and Ontario provinces significantly outperform others. KMS could investigate customer behaviors or marketing efforts in these regions for possible replication elsewhere.

- **Customer Retention**: Top customers consistently buy Office Supplies and Technology. These patterns can be leveraged for loyalty campaigns or upselling.

- **Shipping Misalignment**: Critical orders are often shipped using slower and cheaper methods like Delivery Truck, while lower priority orders use more expensive shipping like Express Air — indicating poor alignment between shipping cost and urgency.

- **Bottom Customers**: 10 customers showed sales below ₦500, with limited repeat purchases. These customers could be targeted with offers, surveys, or re-engagement strategies.


## 8. Recommendations

-  **Prioritize Inventory Stocking** for Technology and Office Supplies to maintain supply for high-demand categories.
  
-  **Review Shipping Policy** to align shipping cost with order urgency — avoid overspending on low-priority orders.

-  **Run Loyalty Campaigns** for top 10 customers with customized product recommendations.

- **Conduct Outreach to Low-Spending Customers** to understand low engagement and offer incentives.

- **Explore Expansion in High-Performing Regions** like West and Ontario with aggressive regional marketing.

---

## 9. Limitations

- Data only covers 2009–2012, and may not reflect current customer behavior.
- Return reasons are not included, limiting full customer service analysis.
- Some customer segment classifications may be ambiguous without proper documentation.



## 10.Sample SQL Queries Used

Here are some of the core SQL queries that powered the analysis, written for Microsoft SQL Server (T-SQL):


### Case Scenario I

**1. Product category with highest sales**
```sql
SELECT TOP 1 Product_Category, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Product_Category
ORDER BY TotalSales DESC

2. Top 3 and Bottom 3 regions by total sales

-- Top 3
SELECT TOP 3 Region, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Region
ORDER BY TotalSales DESC

-- Bottom 3
WITH RegionSales AS (
    SELECT Region, SUM(Sales) AS TotalSales
    FROM KMS
    GROUP BY Region)
SELECT TOP 3 *
FROM RegionSales
ORDER BY TotalSales ASC

3. Total sales of appliances in Ontario

SELECT SUM(Sales) AS TotalApplianceSales
FROM KMS
WHERE Product_Sub_Category = 'Appliances' AND Province = 'Ontario';

4. Bottom 10 customers by sales

SELECT TOP 10 Customer_Name, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Customer_Name
ORDER BY TotalSales ASC

5. Shipping method with the highest shipping cost

SELECT TOP 1 Ship_Mode, SUM(Shipping_Cost) AS TotalShippingCost
FROM KMS
GROUP BY Ship_Mode


### Case Scenario II

6. Top 5 customers by sales

SELECT TOP 5 Customer_Name, SUM(Sales) AS TotalSales
FROM KMS
GROUP BY Customer_Name
ORDER BY TotalSales DESC

6b. What those top customers typically buy

SELECT Product_Category, COUNT(*) AS PurchaseCount
FROM KMS
WHERE Customer_Name IN (
    'Emily Phan', 
    'Deborah Brumfield', 
    'Roy Skaria', 
    'Sylvia Foulston', 
    'Grant Carroll'
)
GROUP BY Product_Category
ORDER BY PurchaseCount DESC

7. Small business customer with highest sales

SELECT TOP 1 Customer_Name, SUM(Sales) AS TotalSales
FROM KMS
WHERE Customer_Segment = 'Small Business'
GROUP BY Customer_Name
ORDER BY TotalSales DESC

8. Corporate customer with most orders (2009–2012)

SELECT TOP 1 Customer_Name, COUNT(Order_ID) AS OrderCount
FROM KMS
WHERE Customer_Segment = 'Corporate'
  AND YEAR(Order_Date) BETWEEN 2009 AND 2012
GROUP BY Customer_Name
ORDER BY OrderCount DESC

9. Most profitable consumer customer

SELECT TOP 1 Customer_Name, SUM(Profit) AS TotalProfit
FROM KMS
WHERE Customer_Segment = 'Consumer'
GROUP BY Customer_Name
ORDER BY TotalProfit DESC

10. Customers who returned items

SELECT DISTINCT k.Customer_Name, k.Customer_Segment
FROM KMS k
JOIN order_status o ON k.Order_ID = o.Order_ID
WHERE o.Status = 'Returned'

11. Was shipping mode aligned with order priority?

SELECT Order_Priority, Ship_Mode, 
       COUNT(*) AS NumOrders,
       AVG(Shipping_Cost) AS AvgShippingCost
FROM KMS
GROUP BY Order_Priority, Ship_Mode
ORDER BY Order_Priority, Ship_Mode;


## 📬 Contact

**Analyst**: Kingsley Omodigono Oghenekaro
**Role**: Business Intelligence Analyst  
**Tools Used**: SSMS, Excel.  
**Email**: omleysplufic@gmail.com
