# Data Analysis for Maven Market: Retail Analytics and Optimization

Data analysis is the first step in Power BI, where I dive deep into the data to uncover key patterns using DAX measures.

I start by connecting the cleaned and transformed PostgreSQL data to Power BI. Next, I verify that all date columns are in the correct short date format and ensure that all monetary values are properly formatted as currency. Since the relationships between tables were already defined in PostgreSQL, Power BI automatically establishes the connections.

Following this, I focus on the key questions that guide the analysis. These questions are derived from the foundational DAX measures and will help in gaining valuable insights from the data. The questions explore various aspects of the business such as executive summary, product performance, customer behavior, and operational efficiency. Here are the key questions for each area:

1. [Foundational Calculations](#foundational-calculations)
2. [Executive Summary](#executive-summary)
3. [Product Performance](#product-performance)
4. [Customer Behavior](#customer-behavior)
5. [Operational Efficiency](#operational-efficiency)


### Foundational Calculations
- How do key metrics like total orders, total sold, and total returns vary across different dimensions?
- What do total revenue and cost metrics reveal about profitability?
- How does total profit correlate with other performance metrics?

**Note**: These measures will be used in subsequent sections to analyze executive summary, product performance, customer behavior, and operational efficiency.

Below are the DAX measures.
```DAX
Total Orders = COUNT(Orders[quantity])

Total Sold = SUM(Orders[quantity])

Total Returns = SUM(Returns[quantity])
 
Total Revenue = SUMX(Orders,Orders[quantity] * RELATED(Products[product_retail_price]))

Total Cost = SUMX(Orders,Orders[quantity] * RELATED(Products[product_cost]))

Total Profit = [Total Revenue] - [Total Cost]
```


### Executive Summary
- What is the overall sales trend?
- What is the revenue trend over time?
- Which country generates the most revenue?
- Which product brand has the highest revenue?
- Which product brand has the highest profit margin?

Below are the DAX measures.
```DAX
Total Sold = SUM(Orders[quantity])

Total Revenue = SUMX(Orders,Orders[quantity] * RELATED(Products[product_retail_price]))

Total Profit = [Total Revenue] - [Total Cost]

Profit Margin = DIVIDE([Total Profit],[Total Revenue],0)

Return Rate (%) = DIVIDE([Total Returns], [Total Sold], 0)

YTD Revenue = TOTALYTD([Total Revenue], 'Calendar'[date])

Previous Year YTD Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1, YEAR))
```


### Product Performance
- Which products have the best profit margins and still meet their sales targets?
- Are there any hidden opportunities with products that have high profit margins but lower sales?
- How can Maven Market improve 

Below are the DAX measures.
```DAX
Total Sold = SUM(Orders[quantity])

Total Revenue = SUMX(Orders,Orders[quantity] * RELATED(Products[product_retail_price]))

Total Profit = [Total Revenue] - [Total Cost]

Previous Month Sold = CALCULATE([Total Sold], DATEADD('Calendar'[date], -1, MONTH))

10 % Sold Target = [Previous Month Sold] * 1.1

Sold Target Gap = [Total Sold] - [10 % Sold Target]

Previous Month Revenue = CALCULATE([Total Revenue], DATEADD('Calendar'[date], -1, MONTH)) 

10 % Revenue Target = [Previous Month Revenue] * 1.1

Revenue Target Gap = [Total Revenue] - [10 % Revenue Target]

Previous Month Profit = CALCULATE([Total Profit], DATEADD('Calendar'[date], -1, MONTH))

10 % Profit Target = [Previous Month Profit] * 1.1

Profit Target Gap = [Total Profit] - [10 % Profit Target]

Product Metric Selections = {
			     ("Sold", NAMEOF('Measures Table'[Total Sold]), 0),
    			     ("Revenue", NAMEOF('Measures Table'[Total Revenue]), 1),
                             ("Profit", NAMEOF('Measures Table'[Total Profit]), 2),
                             ("Returns", NAMEOF('Measures Table'[Total Returns]), 3)
                             }
```


### Customer Behavior
- What is the overall trend in customer growth?
- How has customer behavior changed over time?
- What patterns in customer demographics indicate opportunities for growth?

Below are the DAX measures.
```DAX
Total Revenue = SUMX(Orders,Orders[quantity] * RELATED(Products[product_retail_price]))

Total Customers = DISTINCTCOUNT(Orders[customer_id])

Average Revenue per Customer = DIVIDE([Total Revenue],[Total Customers],0)

New Customers = [Total Customers] - [Retained Customers]

Retained Customers = CALCULATE(COUNTROWS(INTERSECT(VALUES(Orders[customer_id]), CALCULATETABLE(VALUES(Orders[customer_id]),DATEADD(Calendar[date], -1, MONTH)))))

Retained Customers (%) = DIVIDE([Retained Customers], [Total Customers], 0) 
```


### Operational Efficiency 
- How does store performance vary across different country?
- Are there specific stores that consistently underperform over time?

Below are the DAX measures.
```DAX
Total Profit = [Total Revenue] - [Total Cost]

Revenue Per SQFT = [Total Revenue] / SUM(Stores[total_sqft])

Order Frequency Per Customer = DIVIDE([Total Orders], [Total Customers], 0)
```
