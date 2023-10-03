# SQL - Power BI Sales Dashboard
## Introduction 
In this project, I have created a dynamic sales dashboard designed to provide you with a comprehensive view of your sales performance through Key Performance Indicators (KPIs), while also giving you the power to delve deeper into your product and customer data.
## Problem Statement 
All sales data is in  7 different fact tables and 2 dimension tables dim_product and dim_customer.

![tables](https://github.com/kushwanthreddyn07/Sales-Dashboard-Power-BI/assets/144375008/2912f4c8-b3b1-450f-9f22-1f8c601de92e)

First, need to extract all the useful data into the Power BI platform.

![datamodel23](https://github.com/kushwanthreddyn07/Sales-Dashboard-Power-BI/assets/144375008/fc97727b-13a2-42df-8cc3-a4e62d11296f)


There are 7 fact tables, and information from each table is needed. The current data model we have is very complex and hard to understand. There are so many relations between all tables and many more relations will be needed to connect the unconnected tables. So, it's better to create a data model with a star schema. Star schemas are straightforward and easy to understand. They have a central fact table connected to dimension tables, making it simple to navigate and query the data. And also star schemas boost performances.

## Skills Demonstrated
The following features were used:-
1. SQL:- Joins, Cte
2. Power BI:- Bookmarks, Buttons, Filters, DAX, Modelling, Measures, Page Navigations, Tab Navigations, Power Query.

## Data Modelling
1. As we have seen in the above picture, we have a messy data model, a clean  and understandable data model must be created. 
In the data model, every table has a relationship with at least one other table except the fact_freight_cost table. If we observe carefully fact_freight_cost table has a market column and the dim_customer table also has a market column, so these two tables can be connected, but freight cost will be changed based on customer and fiscal year. We need to connect the fact_freight_cost table with market and fiscal_year, then we will get the correct relationship.
2. We can take fact_sales_monthly tables as the base table because it has all the sales transaction data, and join all the other tables such that we will only have one fact table with all the required data.

**SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)** 

*-- Joining fact_sales_monthly and fact_act_est tables tables on fiscal_year,product_code,customer_code to fetch forecast quantity of products. Performing left join to fetch all transactions from the fact_sales_monthly table*

3. Using CTEs, as we have to join multiple tables continuously.

**with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code))  
SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year)**

*--Joining cte1(previously joined table) and fact_pre_invoice_deductions tables to fetch pre_invoice_discount_percent of products. Performing left join to fetch all transactions from the left table*



**with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)),  
cte2 as (SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year))  
SELECT * FROM cte2 c2 LEFT JOIN fact_post_invoice_deductions pd USING(date,product_code,customer_code)**

*--Joining cte2(previously joined table) and fact_post_invoice_deductions tables to fetch discount_pct and other_deductions_pct of products. Performing left join to fetch all transactions from the left table*



**with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)),  
cte2 as (SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year)),  
cte3 as (SELECT * FROM cte2 c2 LEFT JOIN fact_post_invoice_deductions pd USING(date,product_code,customer_code))  
SELECT * FROM cte3 LEFT JOIN fact_gross_price gp USING(product_code,fiscal_year)**

*--Joining cte3(previously joined table) and fact_gross_price tables using product_code and fiscal_year to fetch the products gross_price.Performing left join to fetch all transactions from the left table*



**with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)),
cte2 as (SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year)),
cte3 as (SELECT * FROM cte2 c2 LEFT JOIN fact_post_invoice_deductions pd USING(date,product_code,customer_code)),
cte4 as (SELECT * FROM cte3 LEFT JOIN fact_gross_price gp USING(product_code,fiscal_year))
SELECT c4.*,c.market  FROM cte4 c4 LEFT JOIN dim_customer c USING(customer_code)**

*--Joining cte4(previously joined table) and dim_customer tables to fetch the market of customers. We already have the fiscal year column in the cte4 table, if we bring the market column into this table using the customer code column, then I can join the fact_freight_cost table easily. c4* means all columns from the cte4 table and c.market means only market column from dim_customer table. Performing left join to fetch all transactions from the left table*



**with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)),
cte2 as (SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year)),
cte3 as (SELECT * FROM cte2 c2 LEFT JOIN fact_post_invoice_deductions pd USING(date,product_code,customer_code)),
cte4 as (SELECT * FROM cte3 LEFT JOIN fact_gross_price gp USING(product_code,fiscal_year)),
cte5 as (SELECT c4.*,c.market  FROM cte4 c4 LEFT JOIN dim_customer c USING(customer_code))
SELECT * FROM cte5 c5 LEFT JOIN fact_freight_cost fc USING(market,fiscal_year)**

*--Joining cte5(previously joined table) and fact_freight_cost tables using market and fiscal_year to fetch freight_pct and other_cost_pct of products.  Performing left join to fetch all transactions from the left table*



**with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)),
cte2 as (SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year)),
cte3 as (SELECT * FROM cte2 c2 LEFT JOIN fact_post_invoice_deductions pd USING(date,product_code,customer_code)),
cte4 as (SELECT * FROM cte3 LEFT JOIN fact_gross_price gp USING(product_code,fiscal_year)),
cte5 as (SELECT c4.*,c.market  FROM cte4 c4 LEFT JOIN dim_customer c USING(customer_code)),
cte6 as (SELECT * FROM cte5 c5 LEFT JOIN fact_freight_cost fc USING(market,fiscal_year))
SELECT * FROM cte6 c6 LEFT JOIN fact_manufacturing_cost mc USING(product_code,fiscal_year)**

*--Joining cte6(previously joined table) and fact_manufacturing_cost tables using product_code and fiscal_year to fetch manufacturing_cost of products.  Performing left join to fetch all transactions from the left table*

**CREATE VIEW fact_transactions as (with cte1 as (SELECT s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity FROM fact_sales_monthly s LEFT JOIN fact_act_est e USING(fiscal_year,product_code,customer_code)),
cte2 as (SELECT * FROM cte1 c1 LEFT JOIN fact_pre_invoice_deductions pr USING(customer_code,fiscal_year)),
cte3 as (SELECT * FROM cte2 c2 LEFT JOIN fact_post_invoice_deductions pd USING(date,product_code,customer_code)),
cte4 as (SELECT * FROM cte3 LEFT JOIN fact_gross_price gp USING(product_code,fiscal_year)),
cte5 as (SELECT c4.*,c.market  FROM cte4 c4 LEFT JOIN dim_customer c USING(customer_code)),
cte6 as (SELECT * FROM cte5 c5 LEFT JOIN fact_freight_cost fc USING(market,fiscal_year)),
cte7 as (SELECT * FROM cte6 c6 LEFT JOIN fact_manufacturing_cost mc USING(product_code,fiscal_year)
SELECT * FROM cte7 )**

*--Creating view fact_transactionss for storing all the required data that is joined into a single table. We can store it as a table but this data set has around 600k rows, it would be easier to store a view than to store a table with so many columns and rows*

![fact_transaction1](https://github.com/kushwanthreddyn07/Sales-Dashboard-Power-BI/assets/144375008/7e36723e-ab11-4042-ac51-d9c11d80bb01)

Now we have all the required columns from 7 fact tables stored in a single table fact_transactions. We can easily create a star schema data model with fact_transactions as the fact table and dim_product, and dim_customer tables as dimension tables. 





