# Exploratory Data Analysis

Exploratory Data Analysis (EDA) is the first step in Power BI, where I dive deep into the data to uncover key patterns using DAX measures.

I start by connecting the cleaned and transformed PostgreSQL data to Power BI. Next, I verify that all date columns are in the correct short date format and ensure that all monetary values are properly formatted as currency. Since the relationships between tables were already defined in PostgreSQL, Power BI automatically establishes the connections.

Following this, I focus on the key questions that guide the analysis. These questions are derived from the foundational DAX measures and will help in gaining valuable insights from the data. The questions explore various aspects of the business such as overall sales performance, product details, customer behavior, and operational efficiency. Here are the key questions for each area:

1. Foundational Calculations in DAX
2. Sales Topline Performance (DAX + Visual Report)
3. Product Detail (DAX + Visual Report)
4. Customer Detail (DAX + Visual Report)
5. Operational Efficiency (DAX + Visual Report)

### 1. Foundational Calculations in DAX
- How do key metrics like total orders, total sold, and total returns vary across different dimensions?
- What do total revenue and cost metrics reveal about profitability?
- How does total profit correlate with other performance metrics?

Below are the DAX measures.
```DAX
Total Orders = COUNT(Orders[quantity])

Total Sold = SUM(Orders[quantity])

Total Returns = SUM(Returns[quantity])
 
Total Revenue = SUMX(Orders,Orders[quantity] * RELATED(Products[product_retail_price]))

Total Cost = SUMX(Orders,Orders[quantity] * RELATED(Products[product_cost]))

Total Profit = [Total Revenue] - [Total Cost]
```

### 2. Sales Topline Performance (DAX + Visual Report)
- What is the overall sales trend?
- What is the revenue trend over time?
- Which country generates the most revenue?
- Which product brand has the highest revenue?
- Which product brand has the highest profit margin?

Below are the DAX measures.
```DAX
Profit Margin = DIVIDE([Total Profit],[Total Revenue],0)

Return Rate (%) = DIVIDE([Total Returns], [Total Sold], 0)

YTD Revenue = TOTALYTD([Total Revenue], 'Calendar'[date])

Previous Year YTD Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1, YEAR))
```
