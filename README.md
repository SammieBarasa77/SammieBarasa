# Data Project
![Logo](https://github.com/SammieBarasa77/walmart_sales/blob/main/assets/images/cover_final.png)
## The Superstore Product Sales Analysis Project

This project delves into the sales performance of a fictional superstore, offering actionable insights for improved decision-making. Using SQL, Tableau and Power BI, I analyzed key sales metrics, uncovered trends, and addressed pressing sales challenges.

Key Trends and Metrics:

Sales Distribution
Identified product categories and regions that contributed the most and least to total sales.
Profitability Analysis: Revealed high-performing products and segments, as well as those incurring losses.
Customer Trends: Highlighted purchasing behaviours and seasonal sales patterns that drive revenue.

Sales Problems Addressed:
Low Sales in Specific Regions: Pinpointed underperforming regions and recommended strategies such as targeted marketing campaigns and inventory adjustments.
High Return Rates: Analyzed discounting strategies and their impact, guiding to optimize pricing policies and reduce unnecessary returns.
Stockouts in High-Demand Categories: Identified inventory gaps and implemented forecasting models to maintain optimal stock levels.

## Table of Contents

- [Objective](#objective)
- [Data Source](#data-source)
- [Stages](#stages)
- [Design](#design)
  - [Tools](#tools)
- [Development](#development)
  - [Data Exploration](#data-exploration)
  - [Data Cleaning](#data-cleaning)
- [Testing](#testing)
  - [Data Quality Tests](#data-quality-tests)
  - [Aggregating data](#aggregating-data)
- [Basic Sales Analysis](#basic-sales-analysis)
- [Advanced Sales Analysis](#advanced-sales-analysis)
  - [Discount Impacts](#discount-impacts)
  - [Customers' Profits](#customers'-profits)
  - [Orders Analysis](#orders-analysis)
  - [Sales Over Time](#sales-over-time)
  - [Customer and Transaction-Level Analysis](#customer-and-transaction-level-analysis)
  - [High-Values Transactions ](#high-values-transactions)
  - [Aggregated Metrics](#aggregated-metrics)
  - [Segmentation Analysis](#segmentation-analysis)
- [Data Visualization](#data-visualization)
  - [Dashboard](#dashboard)
- [Analysis](#analysis)
  - [Findings](#findings)
- [Recommendations](#recommendations)
  - [Potential ROI](#potential-roi)
  - [Potential Courses of Actions](#potential-courses-of-actions)
- [Conclusion](#conclusion)
- 
## Objective
The primary objective of this project is to analyze Superstore's sales performance to identify trends, optimize inventory, and improve profitability.

## Data Source
The dataset used for this analysis is the Superstore Sales data, which includes transactions, customer information, and product data.
You can find the dataset used for this analysis on Kaggle or here: (Sales/assets/docs/Sample - Superstore.csv)

## Stages
The project follows these stages:
1. Requirements Gathering
2. Data Analysis and Cleaning
3. Testing and Validation
4. Visualization and Recommendations

## Design

### Tools
- SQL for data querying and analysis
- Tableau & Power BI for data visualization 
## Development
This section explains the step-by-step process undertaken to prepare and process the data for analysis.

### Data Exploration

-Descriptive statistics ( mean, median, and total sales)
-Identifying relationships between variables (e.g., sales vs. profit).
-Detecting outliers and irregularities in data distribution.

### Data Cleaning
-Removing duplicates and null values.
```sql
-- Remove duplicate rows based on the 'Order ID' column
DELETE FROM superstore_1
WHERE (`Order_ID`, `Product_ID`) IN (
    SELECT * FROM (
        SELECT `Order_ID`, `Product_ID`
        FROM superstore_1
        GROUP BY `Order_ID`, `Product_ID`
        HAVING COUNT(*) > 1
    ) AS duplicates
);
-- Removing rows with null values in critical fields like Sales, Quantity, Profit, Order Date, or Ship Date.
DELETE FROM superstore_1
WHERE Sales IS NULL
OR Quantity IS NULL
OR Profit IS NULL
OR `Order_Date` IS NULL
OR `Ship_Date` IS NULL
;
```
## Testing
### Data Quality Checks
-Standardizing inconsistent formats (e.g., date and consistency).
-Handling outliers to ensure accurate analysis.
-Creating new calculated fields 
-Structuring the data to align with SQL and dashboard requirements.
```sql
-- Check for Missing Data
SELECT *
FROM superstore_1
WHERE Customer_Name IS NULL OR Sales IS NULL OR Order_Date IS NULL;
-- Data Consistency Check
-- Verify Sales Amount is Positive
SELECT *
FROM superstore_1
WHERE Sales < 0;
-- Check for incorrect date formats 
SELECT *
FROM superstore_1
WHERE Order_Date IS NULL OR Order_Date NOT BETWEEN '2014-01-01' AND '2017-12-31';
```
### Aggregating data (e.g., Max, Min, & Average sales).

```sql
-- Average sales, count of products, total_sales, max and min sales
SELECT Region,
	AVG(Sales), 
	COUNT(Product_ID), 
	MAX(Sales), 
	MIN(Sales)
FROM superstore_1
GROUP BY Region
ORDER BY AVG(Sales)
;
```
-- Output
```sql
-- Aggregations By Region
|Region   | AVG Sales        | COUNT(ProducT_ID) | MAX(Sales) | MIN(Sales)|
|---------|------------------|-------------------|------------|-----------|
| Central |220.2658729203543 | 2260	         | 17499.95   | 0.444     |
| West	  |230.3171638682597 | 3097	         | 13999.96   | 0.99      |
| East    |243.4109490909085 | 2750	         | 11199.968  | 0.852     |
| South	  |246.2337052832593 | 1571	         | 22638.48   | 1.167     |
```

## Basic Sales Analysis
```sql
SELECT Region, SUM(Sales) AS Total_Sales
FROM superstore_1
GROUP BY Region
ORDER BY Total_Sales DESC;

-- Top 5 Products by their Sales 
SELECT Product_Name, SUM(Sales) AS Total_Sales
FROM superstore_1
GROUP BY Product_Name
ORDER BY Total_Sales DESC
  
-- Top 5 Customers  their Total Purchases 
SELECT Customer_Name, SUM(Sales) AS Total_Sales
FROM superstore_1
GROUP BY Customer_Name
ORDER BY Total_Sales DESC
LIMIT 5;
```
## Advanced Sales Analysis

```sql
-- Profit trends over time (monthly)
WITH Monthly_Profit AS (
    SELECT DATE_FORMAT(`Order_Date`, '%Y-%m') AS Order_of_Month, 
           SUM(Profit) AS Monthly_Profit
    FROM superstore_1
    GROUP BY Order_of_Month
)
SELECT Order_of_Month, Monthly_Profit
FROM Monthly_Profit
ORDER BY Order_of_Month;
-- Impact of discounts on sales
SELECT Discount, 
       SUM(Sales) AS Total_Sales, 
       SUM(Profit) AS Total_Profit
FROM superstore_1
GROUP BY Discount
ORDER BY Total_Sales DESC;
```
-- Output
```sql
| Order_Of_Month  | Monthly_Profit   |
|-----------------|------------------|
|2014-01	  | 2416.6891        |
|2014-02	  | 781.842799999999 |
|2014-03	  | 419.430999999999 |
|2014-04	  | 3475.07780000000 |
|2014-05	  | 2732.5806        |
|2014-06	  | 5054.63939999999 |
|2014-07	  | -926.10180000000 |
|2014-08          | 5352.99839999999 |
|2014-09          | 8214.68420000000 |
|2014-10	  | 3452.01919999999 |
|2014-11	  | 9181.78249999999 |
|2014-12          | 8912.90069999998 |
```
### Discount Impacts
```sql
-- Impact of discounts on sales
SELECT Discount, 
       SUM(Sales) AS Total_Sales, 
       SUM(Profit) AS Total_Profit
FROM superstore_1
GROUP BY Discount
ORDER BY Total_Sales DESC;
```
-- Output
```sql
|  Discount  |     Total_Sales    |  Total _Profit     |
|------------|--------------------|--------------------|
| 0	     | 1069076.389999991  | 315997.11189999967 |
| 0.2	     | 755427.6480000044  | 89223.29760000008  |
| 0.4  	     | 116345.8560000000  |-23065.44200000001  |
| 0.3        | 102663.910999999   |-10345.15979999999  |
| 0.5	     | 58918.53999999998  |-20506.42809999999  |
| 0.1  	     | 54369.35100000001  | 9029.176999999998  |
| 0.7        | 40022.01600000006  |-39643.722099999955 |
| 0.15	     | 27558.521500000006 | 1418.9915          |
| 0.8	     | 16708.323999999993 |-30136.244799999982 |
| 0.32	     | 14493.458799999999 |-2391.1377000000007 |
| 0.6	     | 6237.400000000002  |-5548.399600000002  |
| 0.45	     | 5484.974           |-2493.1111          |
```
### Customers' Profits
```sql
-- Top 5 customers by their profit margins 
WITH Customer_Profit AS (
    SELECT Customer_Name, 
           SUM(Profit) / SUM(Sales) * 100 AS Profit_Margin
    FROM superstore_1
    GROUP BY Customer_Name
)
SELECT Customer_Name, Profit_Margin
FROM Customer_Profit
ORDER BY Profit_Margin DESC
LIMIT 5;
```
-- Output
```sql
| Customer_Name | Profit_Margin      |
|---------------|--------------------|
| Tamara Chand	| 47.13720816361558  |
| Jenna Caffey	| 46.960430315664084 |
| Raymond Buch	| 46.146321783218596 |
| Steven Roelle	| 45.800198164424934 |
| Bobby Odegard	| 45.442329740885114 |
```
### Orders Analysis
```sql
-- Number of orders per customer
SELECT Customer_Name, COUNT(Order_ID) AS Customer_orders
FROM superstore_1
GROUP BY Customer_Name
ORDER BY Customer_orders DESC
LIMIT 10;

-- Sales Trends over time 
SELECT Order_Date, SUM(Sales) AS Sales_Overtime
FROM superstore_1
GROUP BY Order_Date
ORDER BY Order_Date
;
```
### Sales Over Time 
```sql
-- Daily Trends 
SELECT Order_Date, SUM(Sales) AS Daily_Sales
FROM superstore_1
GROUP BY Order_Date
ORDER BY Order_Date;
-- Weekly Trends 
SELECT WEEK(Sales) AS WeekNumber, SUM(Sales) AS Weekly_Sales
FROM superstore_1
GROUP BY WeekNumber
ORDER BY WeekNumber;
-- Monthly Trends 
SELECT MONTH(Order_Date) AS Month_, SUM(Sales) AS Monthly_Sales
FROM superstore_1
GROUP BY Month_
ORDER BY Month_;
```
### Customer and Transaction-Level Analysis
```sql
-- Transaction Size Distribution (Small vs. Large Transactions)
SELECT 
    CASE 
        WHEN Profit < 10 THEN 'Small'
        WHEN Profit BETWEEN 11 AND 25 THEN 'Medium'
        ELSE 'Large'
    END AS Profit_Size, 
    COUNT(*) AS NumTransactions
FROM superstore_1
GROUP BY Profit_Size;
```
-- Output
```sql
| Profit_Size | NumTransactions |
|-------------|-----------------|
| Large	      | 2890            |
| Small	      | 5196            |
| Medium      | 1592            |
```
### High-Values Transactions 
- This may be outliers
```sql
SELECT * 
FROM superstore_1
WHERE Sales > (SELECT AVG(Sales) + 2 * STDDEV(Sales) 
FROM superstore_1);
```
### Aggregated Metrics
```sql
-- Average Sales Per Day
SELECT AVG(Daily_Avg_Sales) AS Overall_Daily_Avg_Sales
FROM (
    SELECT Order_Date, AVG(Sales) AS Daily_Avg_Sales
    FROM superstore_1
    GROUP BY Order_Date
) AS DailySales
;
```
### Segmentation Analysis
```sql
-- Sales by Weekday/Weekend
SELECT 
    CASE 
        WHEN DAYOFWEEK(Sales) IN (6, 7) THEN 'Weekend'
        ELSE 'Weekday'
    END AS Day_Type, 
    SUM(Sales) AS WeekDay_type_Sales
FROM superstore_1
GROUP BY Day_Type;
```
-- Output
```sql
| Day_Type | WeekDay_type_Sales |
|----------|--------------------|
| Weekday  | 2156550.1640999587 |
| Weekend  | 110756.2262        |
```

-- Sales by time of the day
```sql
SELECT 
    HOUR(Sales) AS Hour_, 
    SUM(Sales) AS Daily_Sales
FROM superstore_1
GROUP BY Hour_
ORDER BY Hour_;
```
## Data Visualization

#### Dashboard
![Power BI Dashboard](https://github.com/SammieBarasa77/SammieBarasa/blob/main/Sales/assets/images/Screenshot%202024-11-19%20224354.png)

Tableau Dashboard
"https://public.tableau.com/app/profile/samuel.barasa/viz/SalesAnalysis_17320471579370/Dashboard2/"

Power BI Dashboard
The **Super Store Product Sales Analysis** dashboard provides insights into sales, profit margins, and customer trends. View the interactive version or download the Power BI file to explore further.

- [View the Dashboard Online](https://Sales/assets/docs/The_Super_Store_Product_Sales_Analysis.pbix/) 
- [Download the Dashboard](Sales/assets/docs/The%20Super%20Store%20Product%20Sales%20Analysis.pbix)

## Analysis
### Findings
Profit Margins:

Customers with the highest profit margins include Tamara Chand, Jenna Caffey, and Raymond Buch, each exceeding a 45% profit margin.
Sales Performance by Region:

The South Region has the highest average sales, indicating strong regional performance, while the Central Region has the lowest.
Discount Analysis:

Discounts above 30% lead to negative profits, suggesting that such discounts erode profitability.
Customer Transactions:

Most transactions fall under the "Small" category, with profits below $10. Only 14% of transactions qualify as "Large" in terms of profit size.
Seasonal Trends:

Profitability peaks during the holiday months of November and December, indicating a seasonal trend that businesses can capitalize on.

## Recommendations
Focus on High-Profit Regions:

Invest more in the South Region and develop targeted campaigns to boost sales in the Central Region.
Adjust Discount Strategies:

Limit discounts to a maximum of 20% to maintain profitability while still encouraging sales.
Upsell and Cross-Sell:

Develop strategies to increase "Medium" and "Large" transactions by upselling high-value products.
Seasonal Campaigns:

Implement promotional campaigns in Q4 to maximize the holiday sales peak.

### Potential ROI
Estimation of the return on investment for implementing the recommendations:

Expected profit growth after addressing identified inefficiencies.
Cost-benefit analysis of suggested strategies.

### Potential Courses of Action
Specific actions stakeholders can take:

Increase focus on high-performing product categories.
Adjust pricing and discount strategies to optimize profitability.
Invest in underperforming regions with high potential.

## Conclusion
This analysis highlights actionable insights into regional performance, discount impacts, and profit trends. By addressing the recommendations, businesses can optimize sales strategies and drive higher profitability.
