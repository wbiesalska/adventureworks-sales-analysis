# Sales Analysis
## Executive Summary
The objective of this project is to build an end-to-end sales analytics report using the AdventureWorks database, an online transaction processing (OLAP) database sample representing a fictitious multinational bicycle manufacturing company. This database was created by Microsoft.

AdventureWorks database: https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms

The goal of this project is to demonstrate proficiency in SQL, data modeling, DAX calculations, interactive visualizations, and translating data into clear business recommendations.

## Tools Used
SQL Server Management Studio (SSMS): used to write and execute SQL queries to extract, clean, and transform relevant data from the database (ETL process).

Power BI: used for both data modeling and visualization.

DAX: used within Power BI to calculate metrics.

## Data Extraction and Cleaning
### DIM_Customer table
```
-- Cleansed DIM_Customer Table --
SELECT 
        c.CustomerKey AS CustomerKey
      ,c.FirstName AS [First Name]
      ,c.LastName AS [Last Name]
      ,c.FirstName + ' ' + c.LastName AS [Full Name]
      ,CASE c.Gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender
      ,c.DateFirstPurchase AS DateFirstPurchase
      ,g.City AS City
  FROM [AdventureWorksDW2025].[dbo].[DimCustomer] AS c
    LEFT JOIN [AdventureWorksDW2025].[dbo].[DimGeography] AS g ON g.GeographyKey = c.GeographyKey
  ORDER BY CustomerKey ASC
```
### DIM_Category table
```
-- Cleansed DIM_Category table --
SELECT 
       p.[ProductKey]
      ,p.[ProductAlternateKey] AS ProductItemCode
      ,[EnglishProductName] AS [Product Name]
      ,ps.[EnglishProductSubcategoryName] AS [Product Subcategory Name]
      ,pc.[EnglishProductCategoryName] AS [Product Category Name]
      ,p.[Color] AS [Product Color]
      ,p.[Size] AS [Product Size]
      ,p.[ProductLine] AS [Product Line]
      ,p.[ModelName] AS [Product Model Name]
      ,p.[EnglishDescription] AS [Product Description]
    ,ISNULL (p.Status, 'Outdated') AS [Product Status]
  FROM [AdventureWorksDW2025].[dbo].[DimProduct] AS p
    LEFT JOIN [AdventureWorksDW2025].[dbo].[DimProductSubcategory] AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey
    LEFT JOIN [AdventureWorksDW2025].[dbo].[DimProductCategory] AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey
ORDER BY p.ProductKey ASC
```
### DIM_Calendar table
```
-- Cleansed DIM_Data Table --
SELECT
    [DateKey]
      ,[FullDateAlternateKey] AS Date
      ,[EnglishDayNameOfWeek] AS Day
      ,[WeekNumberOfYear] AS WeekNo
      ,[EnglishMonthName] AS Month
      ,LEFT([EnglishMonthName], 3) AS MonthShort
      ,[MonthNumberOfYear] AS MonthNo
      ,[CalendarQuarter] AS Quarter
      ,[CalendarYear] AS Year
  FROM [AdventureWorksDW2025].[dbo].[DimDate]
   WHERE CalendarYear >= 2023
```

### FACT_InternetSales table
```
-- Cleansed FACT_InternetSales Tabe --
SELECT 
    [ProductKey]
      ,[OrderDateKey]
      ,[DueDateKey]
      ,[ShipDateKey]
      ,[CustomerKey]
      ,[SalesOrderNumber]
      ,[SalesAmount]
  FROM [AdventureWorksDW2025].[dbo].[FactInternetSales]
    WHERE LEFT(OrderDateKey, 4) >= YEAR(GETDATE()) -2 --- we only bring 2 years of date from extraction
ORDER BY OrderDateKey ASC
```

## Results & Business Recommendations
#### Revenue Concentration
- The “Bikes” category consistently accounts for the vast majority of total revenue (~90-95% in most periods).
- Top-performing models (e.g., Road-150, Mountain-200) alone generate a disproportionate share of sales.
- Within the Bikes category, color preferences are clear: Black and Yellow models dominate the top sellers, while colors like Silver, Red, and Blue contribute significantly less volume and revenue.

Recommendation: Prioritize inventory, marketing budget, and promotions toward high-margin bike models (Mountain-200, Road-250, Touring-1000, Road-350-W, Road-550-W) while monitoring stock levels for top sellers to avoid lost sales, and consider optimizing the color palette by focusing production and promotions on high-demand colors (black, yellow).

#### Customer Behavior
- Average Order Value (AOV) and average items per order remain stable, indicating consistent basket size.
- Customer retention rate varies year-over-year but shows room for improvement (churn ~25–35% depending on cohort).

Recommendation: Launch targeted re-engagement campaigns (email/win-back offers) for customers who purchased in the previous year but not in the current year. Focus on high-value segments (top cities and repeat buyers).

#### Product Portfolio Efficiency
- A significant number of SKUs (especially in Components and Clothing) contribute very low revenue despite occupying catalog space.

Recommendation: Perform a formal product portfolio review – consider phasing out or bundling low-performing items to reduce complexity and improve margins.

## Next Steps
To further enhance the dashboard and deliver even greater business value:

Integrate actual sales targets/budget data for variance analysis and forecasting
Add geographic mapping with country-level aggregation (requires cleaning/enriching city data)
Implement cohort analysis to track customer lifetime value (CLV) over time
Connect to a live data source (e.g., SQL Server or Azure) for real-time reporting
Develop executive-level drill-through reports and automated PDF/email distribution via Power BI Service
Feel free to explore the .pbix file and provide feedback!
— Created as a portfolio project for Data Analyst positions.
