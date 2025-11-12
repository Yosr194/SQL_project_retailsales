# SQL_project_retailsales
**Retail Sales Analysis SQL Project**

## Project Overview
**Project Title:** Retail Sales Analysis  
**Level:** Beginner  
**Database:** project_retailsales

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives
- Set up a retail sales database: Create and populate a retail sales database with the provided sales data.
- Data Cleaning: Identify and remove any records with missing or null values.
- Exploratory Data Analysis (EDA): Perform basic exploratory data analysis to understand the dataset.
- Business Analysis: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup
**Database Creation:** The project starts by creating a database named project_retailsales.  
**Table Creation:** A table named retail_sales is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE project_retailsales;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

**Basic Exploration:**
```sql
-- Record Count
SELECT COUNT(*) FROM retail_sales;

-- Customer Count
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;

-- Category Count
SELECT DISTINCT category FROM retail_sales;
```

**Data Cleaning:**
```sql
-- 1. Searching for rows with potentially missing or zero values
SELECT * FROM `retail_sales`
WHERE transactions_id = 0
OR sale_date IS NULL
OR sale_time IS NULL
OR customer_id = 0
OR gender IS NULL
OR age IS NULL
OR category IS NULL
OR quantity = 0
OR price_per_unit = 0
OR cogs = 0
OR total_sale = 0;

-- 2. Deleting rows with potentially missing or zero values
DELETE FROM `retail_sales`
WHERE transactions_id = 0
OR sale_date IS NULL
OR sale_time IS NULL
OR customer_id = 0
OR gender IS NULL
OR age IS NULL
OR category IS NULL
OR quantity = 0
OR price_per_unit = 0
OR cogs = 0
OR total_sale = 0;
```

### 3. Data Analysis & Findings

**Query 1: Average age of customer for a specific category ('Beauty')**
```sql
SELECT category, ROUND(AVG(age), 2) AS AVG_AGE 
FROM retail_sales 
WHERE category LIKE 'Beauty';
```

**Query 2: Total sales and total orders for each category**
```sql
SELECT category, SUM(total_sale) AS total_sales, COUNT(*) AS total_orders 
FROM retail_sales 
GROUP BY category;
```

**Query 3: TOP 5 customers (based on total sales)**
```sql
SELECT customer_id, SUM(total_sale) AS total_sales 
FROM retail_sales 
GROUP BY customer_id 
ORDER BY total_sales DESC 
LIMIT 5;
```

**Query 4: Number of transactions made by each gender for each category**
```sql
SELECT category, gender, COUNT(gender) AS total_transactions 
FROM retail_sales 
GROUP BY category, gender;
```

**Query 5: Number of orders by shift**
```sql
WITH hourly_sale AS ( 
    SELECT *, 
        CASE 
            WHEN HOUR(sale_time) < 12 THEN 'Morning' 
            WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN 'Afternoon' 
            ELSE 'Evening' 
        END AS shift 
    FROM retail_sales
) 
SELECT shift, COUNT(*) AS total_orders 
FROM hourly_sale 
GROUP BY shift;
```

**Query 6: Number of unique customers by category**
```sql
SELECT category, COUNT(DISTINCT customer_id) AS unique_customer 
FROM retail_sales 
GROUP BY category;
```

**Query 7: All transactions with total_sales > 1000**
```sql
SELECT * FROM retail_sales 
WHERE total_sale > 1000;
```

**Query 8: Orders made on a specific date**
```sql
SELECT * FROM retail_sales 
WHERE sale_date = '2022-11-05';
```

**Query 9: Orders in November 2022 for a specific category ('Clothing') and quantity >= 4**
```sql
SELECT * FROM `retail_sales` 
WHERE sale_date > '2022-11-01' AND sale_date < '2022-11-30' 
AND category LIKE 'Clothing' 
AND (quantity = 4 OR quantity > 4);
```

**Query 10: Average sale for each month for each year**
```sql
SELECT sales_year, sales_month, average_sales, 
    RANK() OVER(PARTITION BY sales_year ORDER BY average_sales DESC) AS monthly_rank 
FROM 
    (
    SELECT YEAR(sale_date) AS sales_year, 
           MONTH(sale_date) AS sales_month, 
           AVG(total_sale) AS average_sales 
    FROM retail_sales 
    GROUP BY YEAR(sale_date), MONTH(sale_date) 
    ) AS monthly_avg;
```

**Query 11: Average sale for each month for each year with highest rank (Rank = 1)**
```sql
SELECT sales_year, sales_month, average_sales 
FROM 
    (
    SELECT sales_year, sales_month, average_sales, 
           RANK() OVER(PARTITION BY sales_year ORDER BY average_sales DESC) AS monthly_rank 
    FROM 
        (
        SELECT YEAR(sale_date) AS sales_year, 
               MONTH(sale_date) AS sales_month, 
               AVG(total_sale) AS average_sales 
        FROM retail_sales 
        GROUP BY YEAR(sale_date), MONTH(sale_date) 
        ) AS monthly_avg
    ) AS t1 
WHERE monthly_rank = 1;
```

## Findings
- **Customer Demographics:** The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions:** Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends:** Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights:** The analysis identifies the top-spending customers and the most popular product categories.


## Conclusion
This project was a wonderful learning experience. It guided me through the full data analysis workflow, teaching me not only SQL syntax but also how to use it to find meaningful insights into customer behavior and product performance.
