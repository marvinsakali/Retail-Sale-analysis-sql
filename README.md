# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `p1_retail_db`

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `p1_retail_db`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
-- creating the database
DROP DATABASE IF EXISTS p1_retail_db;
CREATE DATABASE p1_retail_db;

-- Creating the table with the data
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
(
transactions_id INT PRIMARY KEY,
sale_date DATE,
sale_time TIME,
customer_id INT,
gender VARCHAR(15),
age INT,
category VARCHAR(15),
quantiy INT,
price_per_unit FLOAT,
cogs FLOAT,
total_sale FLOAT

);

```

### 2. Data Exploration & Cleaning

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
-- Data exploration and cleaning
-- records count
SELECT COUNT(*)
FROM retail_sales;

-- Customer Count: Find out how many unique customers are in the dataset.
SELECT 
    COUNT(DISTINCT customer_id) AS unique_customers
FROM
    retail_sales;
    
-- Category Count: Identify all unique product categories in the dataset.
SELECT 
    COUNT(DISTINCT category) AS categories
FROM
    retail_sales;

-- checking the product categories
SELECT DISTINCT
    category
FROM
    retail_sales;

-- Null value check
SELECT 
    *
FROM
    retail_sales
WHERE
    transactions_id IS NULL
        OR sale_date IS NULL
        OR sale_time IS NULL
        OR customer_id IS NULL
        OR gender IS NULL
        OR age IS NULL
        OR category IS NULL
        OR quantiy IS NULL
        OR price_per_unit IS NULL
        OR cogs IS NULL
        OR total_sale IS NULL;
        
-- Delete Null rows
DELETE FROM retail_sales 
WHERE
    transactions_id IS NULL
    OR sale_date IS NULL
    OR sale_time IS NULL
    OR customer_id IS NULL
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantiy IS NULL
    OR price_per_unit IS NULL
    OR cogs IS NULL
    OR total_sale IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT 
    *
FROM
    retail_sales
WHERE
    category = 'clothing' AND quantiy > 3
        AND SUBSTRING(sale_date, 1, 7) = '2022-11';
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
    category,
    SUM(total_sale) AS total_sales,
    COUNT(*) AS total_orders
FROM
    retail_sales
GROUP BY category
ORDER BY 2 DESC;
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT 
    category, AVG(age) AS avg_age
FROM
    retail_sales
WHERE
    category = 'Beauty'
GROUP BY 1
;
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT 
    *
FROM
    retail_sales
WHERE
    total_sale > 1000;
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT DISTINCT
    gender, category, COUNT(transactions_id) as transactions
FROM
    retail_sales
GROUP BY category , gender
ORDER BY 2 DESC;
```

7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
SELECT YEAR(sale_date) as years, MONTH(sale_date) as months, ROUND(AVG(total_sale), 2) as avg_sales
FROM retail_sales
GROUP BY YEAR(sale_date), MONTH(sale_date)
ORDER BY 3 DESC;

--  adding rank
WITH best_selling_month(years, months, avg_sales) AS 
(
SELECT YEAR(sale_date) as years, MONTH(sale_date) as months, ROUND(AVG(total_sale), 2) as avg_sales
FROM retail_sales
GROUP BY years, months
),
best_months AS
(
SELECT years, months, avg_sales, DENSE_RANK() OVER(PARTITION BY  years ORDER BY avg_sales DESC ) as ranking
FROM best_selling_month
)
SELECT years, months, avg_sales, ranking
FROM best_months
WHERE ranking = 1;
```

8. **Write a SQL query to find the top 5 customers based on the highest total sales **:
```sql
SELECT DISTINCT
    customer_id, SUM(total_sale)
FROM
    retail_sales
GROUP BY customer_id
ORDER BY 2 DESC
LIMIT 5;

```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_shift AS
(
SELECT *,
	CASE 
    WHEN HOUR(sale_time) < 12 THEN 'Morning'
    WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
    END as shift
FROM retail_sales
) 
SELECT shift, COUNT(*) as orders
FROM hourly_shift
GROUP BY shift;

SELECT *
FROM retail_sales
;

```
11. **Write a SQL query to find the top 10 customers who have made the most purchases:
```sql
SELECT DISTINCT
    customer_id, COUNT(transactions_id) AS total_orders
FROM
    retail_sales
GROUP BY customer_id
ORDER BY 2 DESC
LIMIT 10;
```

## Findings

- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.



## Author -Marvin Sakali

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!






