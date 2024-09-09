# NIGERIA-AGRICULTURAL-EXPORT(2021-2023)
![](export.jpg)
---

### Project Overview : Analysing The Nigerian Agricultural Export (2021-2023)

This project focuses on analyzing Nigeria's agricultural exports, utilizing a dataset that captures detailed information on various products exported, the companies involved, and the financial metrics associated with these exports. The dataset contains crucial variables such as product names, export destinations, transportation modes, and economic figures like cost of goods sold (COGS), profit, and export value. By exploring this dataset, we aim to uncover insights into Nigeria's agricultural export patterns, identify key trends, and highlight areas for potential growth or improvement.

### Objectives
 **Analyze Export Patterns**:
   - To examine the patterns of agricultural exports from Nigeria, focusing on the frequency, volume, and types of products exported.
    
**Identify Key Export Destinations**:
   - To determine the major export destinations for Nigeria's agricultural products and analyze the trends in these markets over time.
     
**Evaluate Financial Performance**:
   - To assess the financial metrics associated with Nigeria's agricultural exports, including cost of goods sold (COGS), profit margins, and overall export value.
     
**Spot Growth Opportunities**:
   - Monitor and analyze performance trends over time(Montly Basis) 
     
**Company Performance Analysis**:
   - To evaluate the performance of companies involved in agricultural exports, focusing on their contribution to the overall export value and profitability.
     
**Recommendations**:
   - To provide actionable insights and recommendations based on the analysis of export trends, financial metrics, and market destinations.

## Tool
- MySQL
- Power BI
  
## Data Source
- Data-World

## Data Cleaning 
_Identifying and Understanding the Data_
- Reviewing the Dataset: Understanding the structure of the dataset
- Identifying Key Columns: This is to determine which columns are critical to the analysis, such as primary keys, foreign keys, and important metrics.

_Handling Duplicates_
- There were no duplicate data

_Handling Missing Data_
- There were no missing data
 
_Handling Outliers and Removing Irrelevant Data_
- There were no outliers as the data was a clean one

## Data Information
The dataset contains information on Nigeria's agricultural exports, including details about the products exported, the companies involved, and various financial metrics. 
- Number of Entries: 1,000 rows
- Number of Columns: 10

## Data Modelling

## **ANALYSIS ON EXPORT PATTERNS** 
**1. Frequency of Exports**
- To determine how frequently each product is exported
```
SELECT
    Product_Name,COUNT(*) AS Export_Frequency
FROM nigeria_agricultural_export
GROUP BY Product_Name
ORDER BY Export_Frequency DESC;

```
---
![](Export_freq.png)
---

**2. Volume of Exports by Country**
- Identifying the top 10 product-country pairs with the highest export volumes in terms of units sold.

```
SELECT Product_name, `Export Country`, SUM(`Units Sold`) as Total_units
FROM credit.nigeria_agricultural_exports
GROUP BY `Export Country`,Product_name
ORDER BY total_units DESC
LIMIT 10;
```
---
![](TOP_10.png)
---

**3 Most Exported Product**
- To Determine the Most Frequent product exported including their export Value
```
SELECT Product_Name, 
       SUM(`Export Value`) AS Total_Export_Value
FROM credit.nigeria_agricultural_exports
GROUP BY Product_Name
ORDER BY Total_Export_Value DESC;

```
---
![](Mostexported.png)
---
**4. Major Export Destinations**
-Identify the top 10 countries with the highest total export values from Nigeria
```
SELECT `Export Country`, 
       SUM(`Export Value`) AS Total_Export_Value
FROM credit.nigeria_agricultural_exports
GROUP BY `Export Country`
ORDER BY Total_Export_Value DESC
LIMIT 10;  -- Top 10 export destinations
```
---
![](Countries.png)
---


**5.Annual Financial Performance**
- Assessing the yearly financial performance of Nigeria's agricultural exports, including total export value, COGS, profit, and profit margin.
```
SELECT 
    YearlyData.Year,
    ROUND(SUM(YearlyData.Total_Export_Value), 2) AS Total_Export_Value,
    ROUND(SUM(YearlyData.Total_COGS), 2) AS Total_COGS,
    ROUND(SUM(YearlyData.Profit), 2) AS Profit,
    ROUND(
        (SUM(YearlyData.Profit) / NULLIF(SUM(YearlyData.Total_Export_Value), 0)) * 100,
        2
    ) AS Profit_Margin_Percentage
FROM (
    SELECT 
        YEAR(Date) AS Year,
        SUM(`Export Value`) AS Total_Export_Value,
        SUM(COGS) AS Total_COGS,
        SUM(`Export Value` - COGS) AS Profit
    FROM credit.nigeria_agricultural_exports
    GROUP BY YEAR(Date)
) AS YearlyData
GROUP BY YearlyData.Year

```
---
![](Annuall.png)
---

**6. Export Performance**
- Analyzing export trends and performance on a Monthly Basis
```
SELECT 
    Product_Name,
    YEAR(Date) AS Year,
    MONTH(Date) AS Month,
    MONTHNAME(Date) AS Month_Name,
    ROUND(SUM(`Export Value`), 2) AS Total_Export_Value
FROM credit.nigeria_agricultural_exports
GROUP BY 
    Product_Name, 
    YEAR(Date), 
    MONTH(Date), 
    MONTHNAME(Date)
ORDER BY 
    Product_Name, 
    Year, 
    Month;
```
---
![](Perfomance_sale.png)
---

**7. Company Performance Analysis**
- Determining the total export value contributed by each company
```
WITH AggregatedExportValues AS (
    SELECT 
        Company,
        ROUND(SUM(`Export Value`), 2) AS Total_Export_Value
    FROM credit.nigeria_agricultural_exports
    GROUP BY Company
),
FormattedExportValues AS (
    SELECT
        Company,
        Total_Export_Value
    FROM AggregatedExportValues
)
SELECT
    Company,
    Total_Export_Value
FROM FormattedExportValues
ORDER BY Total_Export_Value DESC;
```
---
![](c_contribution.png)
---

- Rank of Companies Based on Profitabilty
```
WITH CompanyExportData AS (
    SELECT 
        Company,
        ROUND(SUM(`Export Value`), 2) AS Total_Export_Value,
        ROUND(SUM(COGS), 2) AS Total_COGS
    FROM credit.nigeria_agricultural_exports
    GROUP BY Company
),
CompanyProfitData AS (
    SELECT
        Company,
        Total_Export_Value,
        Total_COGS,
        ROUND(Total_Export_Value - Total_COGS, 2) AS Total_Profit
    FROM CompanyExportData
)
SELECT
    Company,
    Total_Export_Value,
    Total_Profit
FROM CompanyProfitData
ORDER BY Total_Profit DESC;
```
---
![](TOPCOM.png)
---
- Compare the performance of companies involved in agricultural exports on a monthly basis, focusing on their total export value and profitability.
```
WITH ExportData AS (
    SELECT 
        Company,
        YEAR(Date) AS Year,
        MONTH(Date) AS Numeric_Month,
        MONTHNAME(Date) AS Month_Name,
        ROUND(SUM(`Export Value`), 2) AS Total_Export_Value,
        ROUND(SUM(COGS), 2) AS Total_COGS
    FROM credit.nigeria_agricultural_exports
    GROUP BY Company, YEAR(Date), MONTH(Date)
),
ProfitData AS (
    SELECT
        Company,
        Year,
        Numeric_Month AS Month,
        Month_Name,
        Total_Export_Value,
        ROUND(Total_Export_Value - Total_COGS, 2) AS Total_Profit
    FROM ExportData
)
SELECT
    Company,
    Year,
    Month,
    Month_Name,
    Total_Export_Value,
    Total_Profit
FROM ProfitData
ORDER BY Company, Year, Month;
```
---
![](CPT.png)
---

## FINDINGS AND RECOMMENDATION 









