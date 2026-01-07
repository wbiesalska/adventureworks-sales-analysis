# Sales Analysis
## Executive Summary
The objective of this project is to build an end-to-end sales analytics report using the AdventureWorks database, an online transaction processing (OLAP) database sample representing a fictitious multinational bicycle manufacturing company. This database was created by Microsoft.

AdventureWorks database: https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms

The goal of this project is to demonstrate proficiency in SQL, data modeling, DAX calculations, interactive visualizations, and translating data into clear business recommendations.

## Tools Used
- SQL Server Management Studio (SSMS): used to write and execute SQL queries to extract, clean, and transform relevant data from the database (ETL process).
- Power BI: used for both data modeling and visualization.
- DAX: used within Power BI to calculate metrics.

## Data Cleansing & Transformation (SQL)
Below are the SQL statements for cleansing and transforming necessary data.
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

## Data Model
Below is a screenshot of the data model after cleansed and prepared tables were read into Power BI.

<img width="929" height="630" alt="image" src="https://github.com/user-attachments/assets/b0b90d70-f412-4a45-a217-2fdc4a455f27" />


## Sales Management Dashboard
<img width="1219" height="748" alt="image" src="https://github.com/user-attachments/assets/b39301e2-fe5a-4008-b189-986d1e960c7b" />
<img width="1221" height="748" alt="image" src="https://github.com/user-attachments/assets/de51fc82-89dc-43e1-9fcc-66be3bcf6dad" />
<img width="1217" height="748" alt="image" src="https://github.com/user-attachments/assets/985fa4e8-2753-4994-b2bb-3b20f0e87281" />




## Results & Business Recommendations

#### Revenue Concentration
- In spite of the highest sales quantity placed for "Accessories", the "Bikes" category consistently accounts for the vast majority of total revenue (~90-95% in most periods).
- Top-performing models of bikes (Road-250, Mountain-200, Road-650) alone generate a disproportionate share of sales.
- Within the "Bikes" category, color preferences are clear: black and yellow models dominate the top sellers, while colors like silver, red, and blue contribute significantly less volume and revenue.

Recommendation: Prioritize inventory, marketing budget, and promotions toward high-margin bike models (Road-250, Mountain-200, Road-650) while monitoring stock levels for top sellers to avoid lost sales, and consider optimizing the color palette by focusing production and promotions on high-demand colors (black, yellow).

#### Customer Behavior
- Average Order Value (AOV) decreased from 2023 to 2024, primarily due to the successful introduction of lower-priced accessories and clothing. This shift should be viewed positively, as it coincided with an ~8-fold increase in order volume, higher average items per order (>2), and overall revenue growth.
- Customer retention rate shows a positive trend, improving year-over-year (rising to approximately 29% in 2024), suggesting growing customer loyalty.

Recommendation: Launch targeted re-engagement campaigns (email/win-back offers) for customers who purchased in the previous year but not in the current year. Focus on high-value segments (top cities (London, Paris, Bellflower, Berlin) and repeat buyers).

#### Key Sales Hubs
- London and Paris generate the highest sales (over 1.1M) and customer volume, clearly standing out as primary regional sales centers.

Recommendation: Increase investment in London and Paris through targeted local campaigns and expanded sales capacity to maximize revenue potential.

#### Product Portfolio Expansion & Cross-Selling Success
- In 2023, sales were restricted to the Bikes category only, limiting volume and revenue potential.
- The 2024 launch of Accessories and Clothing drove explosive growth: dramatically higher product quantities sold, an 8-fold surge in orders, and a substantial total revenue increase. The rise in average items per order (>2) strongly indicates effective cross-selling, with bike buyers frequently adding accessories/clothing.
- A significant number of SKUs ("Components" category) contribute 0 revenue despite occupying catalog space.

Recommendation: Build on this cross-selling momentum by creating curated bundles (e.g., bike + helmet, bottle, or apparel kits) to boost AOV further. Review the portfolio to expand high-velocity accessory lines while pruning underperforming SKUs ("Components" category).


Created as a portfolio project for Data Analyst positions.
