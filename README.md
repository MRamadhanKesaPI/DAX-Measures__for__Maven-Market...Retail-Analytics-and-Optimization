# Exploratory Data Analysis

Exploratory Data Analysis (EDA) is the first step in Power BI, where I dive deep into the data to uncover key patterns using DAX measures.

I start by connecting the cleaned and transformed PostgreSQL data to Power BI. Next, I verify that all date columns are in the correct short date format and ensure that all monetary values are properly formatted as currency. Since the relationships between tables were already defined in PostgreSQL, Power BI automatically establishes the connections.

Following this, I focus on the key questions that guide the analysis. These questions are derived from the foundational DAX measures and will help in gaining valuable insights from the data. The questions explore various aspects of the business such as overall sales performance, product details, customer behavior, and operational efficiency. Here are the key questions for each area:

1. Foundational Calculations in DAX
2. Sales Topline Performance (DAX + Visual Report)
3. Product Detail (DAX + Visual Report)
4. Customer Detail (DAX + Visual Report)
5. Operational Efficiency (DAX + Visual Report)


### 1. Foundational Measures
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


### 2. Sales Topline Performance 
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


### 3. Product Detail 
- Which products have the best profit margins and still meet their sales targets?
- Are there any hidden opportunities with products that have high profit margins but lower sales?
- How can Maven Market improve 

Below are the DAX measures.
```DAX
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


### 4. Customer Detail
- What is the overall trend in customer growth?
- How has customer behavior changed over time?
- What patterns in customer demographics indicate opportunities for growth?

Below are the DAX measures.
```DAX
Total Customers = DISTINCTCOUNT(Orders[customer_id])

Average Revenue per Customer = DIVIDE([Total Revenue],[Total Customers],0)

New Customers = [Total Customers] - [Retained Customers]

Retained Customers = CALCULATE(COUNTROWS(INTERSECT(VALUES(Orders[customer_id]), CALCULATETABLE(VALUES(Orders[customer_id]),DATEADD(Calendar[date], -1, MONTH)))))

Retained Customers (%) = DIVIDE([Retained Customers], [Total Customers], 0) 
```


### 5. Operational Efficiency 
- How does store performance vary across different country?
- Are there specific stores that consistently underperform over time?

Below are the DAX measures.
```DAX
Revenue Per SQFT = [Total Revenue] / SUM(Stores[total_sqft])

Order Frequency Per Customer = DIVIDE([Total Orders], [Total Customers], 0)
```
