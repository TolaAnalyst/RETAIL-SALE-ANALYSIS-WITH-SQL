# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Database**: `RETAIL PROJECT`

This project demonstrates core SQL skills used in data analysis, including database setup, data import, data cleaning, exploratory data analysis (EDA), and business-driven analysis. The project works with a retail sales dataset imported from a CSV file and answers real-world business questions using SQL.

---

## Objectives

1. Set up a retail sales table in SQL Server.
2. Import retail sales data from a CSV file.
3. Clean and validate the dataset.
4. Perform exploratory data analysis (EDA).
5. Answer key business questions using SQL queries.

---

## Project Structure

### 1. Database & Table Setup

The project uses an existing database named **RETAIL PROJECT** and creates a table called **retail** to store sales data.

```sql
/* Create a table*/

use [RETAIL PROJECT ]
CREATE TABLE 
retail (

transactions_id int ,
sale_date date ,
sale_time	time, 
customer_id	int ,
gender	varchar (15),
age	int ,
category varchar (25),
quantity	int ,
price_per_unit	float,
cogs	float,
total_sale float

)
```

---

### 2. Data Import from CSV

Retail sales data is imported from a CSV file using the `BULK INSERT` command.

```sql
/* IMPORT DATA FROM THE CSV FILE*/

BULK INSERT retail
FROM "C:\Users\HP\Downloads\SQL_RETAIL.csv"
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,       
    FIELDTERMINATOR = ',', 
    ROWTERMINATOR = '\n'
);

SELECT *
FROM retail
```

---

### 3. Data Cleaning

#### Sale Time Formatting

The `sale_time` column is cleaned to ensure proper time formatting.

```sql
/* DATA CLEANING
Clean sale_time column and adjust to proper time display */

ALTER TABLE retail
ADD sale_time_clean TIME(0);

UPDATE retail
SET sale_time_clean = CAST(sale_time AS TIME(0));

ALTER TABLE retail DROP COLUMN sale_time;

EXEC sp_rename 
    'retail.sale_time_clean', 
    'sale_time', 
    'COLUMN';
```

---

### 4. Null Value Check & Removal

Rows containing NULL values are first identified and then removed to maintain data quality.

```sql
/* Check for Null values in the table*/

SELECT *
FROM retail
where transactions_id is null
    or sale_date is null
    or sale_time	is null
    or customer_id	is null
    or gender	is null
    or age	is null
    or category	is null
    or quantity	is null
    or price_per_unit is null
    or cogs	is null
    or total_sale is null


delete from retail
where transactions_id is null
    or sale_date is null
    or sale_time	is null
    or customer_id	is null
    or gender	is null
    or age	is null
    or category	is null
    or quantity	is null
    or price_per_unit is null
    or cogs	is null
    or total_sale is null
```

---

### 5. Exploratory Data Analysis (EDA)

```sql
/* Data Exploration

What is the total number of sales */

select count(total_sale) as sum_sales 
from retail

-- What is the total number of  unique customers

select * from retail

select count(distinct customer_id) as total_customer
from retail

-- what is the number of categories in the business

select distinct category
from retail
```

---

## Business Analysis & SQL Queries

### Q1. Sales made on 2022-11-05

```sql
select *
from retail
where sale_date = '2022-11-05'
```

### Q2. Clothing transactions with quantity ≥ 4 in Nov 2022

```sql
select *
FROM RETAIL
WHERE category = 'clothing' 
    and quantity >= 4
    and sale_date between '2022-11-01' and '2022-11-30'
```

### Q3. Total sales for each category

```sql
select category, 
        sum(total_sale) as net_sales, 
        count(*) as total_orders
from retail
group by category 
```

### Q4. Average age of customers in Beauty category

```sql
select AVG(age) as avg_age
from retail
where category = 'beauty'
```

### Q5. Transactions with total sales greater than 1000

```sql
Select * 
from retail
where total_sale > '1000'
```

### Q6. Transactions by gender and category

```sql
select Category, 
        gender,
        count(*) as total_transaction
       
from retail
group by
        category,
        gender
order by 1
```

### Q7. Average monthly sales and best-selling month per year

```sql
select year(sale_date) as Year,
       Month(sale_date) as Month,
       avg(total_sale) as avg_sale,
       rank () over( partition by year (sale_date) order by avg(total_sale) desc) as rank
from retail 
GROUP BY YEAR(sale_date), MONTH(sale_date);
```

```sql
select *
From (
        select year(sale_date) as Year,
       Month(sale_date) as Month,
       avg(total_sale) as avg_sale,
       rank () over( partition by year (sale_date) order by avg(total_sale) desc) as rank
from retail 
GROUP BY YEAR(sale_date), MONTH(sale_date)
) as T1

where Rank = '1'
```

### Q8. Top 5 customers by total sales

```sql
select top (5) customer_id, 
       sum(total_sale) as total_sales
from retail
group  by customer_id
order by 1 asc
```

### Q9. Unique customers per category

```sql
Select category , 
        count( distinct customer_id) as no_of_customers
from retail 
Group by category
```

### Q10. Sales shift analysis

```sql
select *,
    Case
        when Datepart(Hour,sale_time) < 12 then 'Morning'
        when Datepart(Hour, sale_time) between 12 and 17 then 'afternoon'
        else 'evening'
    end as 'shift'
from retail
```

```sql
With hourly_sale
AS
(
select *,
    Case
        when Datepart(Hour,sale_time) < 12 then 'Morning'
        when Datepart(Hour, sale_time) between 12 and 17 then 'afternoon'
        else 'evening'
    end as 'shift'
from retail
)
select 
       shift, count(*) as total_orders 
from hourly_sale
group by shift
```

---

## Findings

- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.


## Author - Talabi Ezekiel A.

