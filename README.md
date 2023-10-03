# SQL - Power BI Sales Dashboard
## Introduction 
In this project, I have created a dynamic sales dashboard designed to provide you with a comprehensive view of your sales performance through Key Performance Indicators (KPIs), while also giving you the power to delve deeper into your product and customer data.
## Problem Statement 
All sales data is in  7 different fact tables and 2 dimension tables dim_product and dim_customer.

![tables](https://github.com/kushwanthreddyn07/Sales-Dashboard-Power-BI/assets/144375008/2912f4c8-b3b1-450f-9f22-1f8c601de92e)

First, need to extract all the useful data into the Power BI platform.

![datamodel1](https://github.com/kushwanthreddyn07/Sales-Dashboard-Power-BI/assets/144375008/4e19085a-2802-45e3-974f-08e9c801c0e9)


There are 7 fact tables, and information from each table is needed. The current data model we have is very complex and hard to understand. There are so many relations between all tables and many more relations will be needed to connect the unconnected tables. So, it's better to create a data model with a star schema. Star schemas are straightforward and easy to understand. They have a central fact table connected to dimension tables, making it simple to navigate and query the data. And also star schemas boost performances.

## Skills Demonstrated
The following features were used:-
1. SQL:- Joins, Cte
2. Power BI:- Bookmarks, Buttons, Filters, DAX, Modelling, Measures, Page Navigations, Tab Navigations, Power Query.

## Data Modelling
1. As we have seen in the above picture, we have a messy data model, a clean  and understandable data model must be created. 
In the data model, every table has a relationship with at least one other table except the fact_freight_cost table. If we observe carefully fact_freight_cost table has a market column and the dim_customer table also has a market column, so these two tables can be connected, but freight cost will be changed based on customer and fiscal year. We need to connect the fact_freight_cost table with market and fiscal_year, then we will get the correct relationship.
2. We can take fact_sales_monthly tables as the base table because it has all the sales transaction data, and join all the other tables such that we will only have one fact table with all the required data.

select s.date,s.fiscal_year,s.product_code,s.customer_code,s.sold_quantity,e.forecast_quantity from fact_sales_monthly s join fact_act_est e using(date,fiscal_year,product_code,customer_code) 
where fiscal_year in (2018,2019,2020)




