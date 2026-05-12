# Retail Sales Analysis SQL Project-1

## Project Overview

**Project Title**: Retail Sales Analysis  
**Database**: `Project1_Sql`

This project demonstrates SQL techniques used in real-world data analytics. It covers database setup, data cleaning, exploratory data analysis (EDA), and business problem solving through SQL queries.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `Project1_Sql`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction_ID, sale_date, sale_time, customer_ ID, gender, age, product_category, quantity_sold, price_per_unit, cost_of_goods_sold (COGS), and total_sale amount.

```sql
-- [TO CREATE DATABASE] --
CREATE DATABASE Project1_Sql;

-- [TO CREATE TABLE] --
CREATE TABLE retail_sales
(	
	transactions_id INT PRIMARY KEY ,
	sale_date DATE,
	sale_time TIME,
	customer_id INT,
	gender VARCHAR(15),
	age	INT,
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
-- [PRINT ALL THE DATA OF TABLE UPTO 10 DATA]
select * from retail_sales LIMIT 10; 

-- [COUNT THE TOTAL NO OF ROWS] --
select COUNT(*) AS TOTAL_ROWS FROM retail_sales;


-- 			[DATA EXPLORATION]

-- Q)How many sales we have?
SELECT count(*) AS total_Sales FROM retail_sales;

-- Q)How many customers we have? (duplicate also)
SELECT count(customer_id) AS total_Customers FROM retail_sales;

-- Q)How many unique customers we have? (duplicate not allowed)
SELECT count( DISTINCT customer_id) AS total_Customers FROM retail_sales;

-- Q)How many category we have? (duplicate also)
SELECT count(category) AS total_Category FROM retail_sales;

-- Q)How many unique category we have? (duplicate not allowed)
SELECT count( DISTINCT category) AS total_Customers FROM retail_sales;

-- Q)Display distinct category names we have? (duplicate not allowed)
SELECT DISTINCT category AS Categories FROM retail_sales;


--        	[DATA CLEANING]

-- [DISPLAY THE ROW IF THERE IS NULL VALUES] --
SELECT * FROM retail_sales
WHERE
   transactions_id IS NULL;

--		OR

--  [DISPLAY THE ROW WHICH HAS NULL VALUES] --
SELECT * FROM retail_sales
WHERE
		transactions_id IS NULL
		OR
	 	sale_date IS NULL
		OR
		sale_time IS NULL
 	    OR
		age IS NULL;
    
-- [DELETE NULL VALUES DATA FROM TABLE] --
SET SQL_SAFE_UPDATES = 0; -- OFF THE SAFE UPDATE MODE 
DELETE FROM retail_sales
WHERE
	transactions_id IS NULL
	OR
	sale_date IS NULL
	OR
	sale_time IS NULL
	OR
	age IS NULL;
SET SQL_SAFE_UPDATES = 1; -- START THE SAFE UPDATE MODE
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

 **Q1.Write a sql query to retrieve all columns for sales made on 2022-11-05.**:
```sql
SELECT * FROM retail_sales 
WHERE	
	sale_date = "2022-11-05";
```

 **Q2.Write a sql query to retrieve all transactions where the category is 'clothing' and the quantity sold is more than 2 in month of Nov-2022.**:
```sql
SELECT * FROM retail_sales
WHERE
	category="Clothing"
    AND
    quantiy >= 2
    AND
    MONTH(sale_date)=11 AND YEAR(sale_date)=2022;
```

 **Q3.Write a sql query to calculate the total sales(total_sale) for each category.**:
```sql
SELECT 
	category, SUM(total_sale) AS total_sales, COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

 **Q4.Write a sql query to find average age of customers who purchased items from the "Beauty" category.**:
```sql
SELECT 
	 ROUND(avg(age),2) AS avg_Age 
FROM retail_sales
WHERE 
	category="Beauty";
```

 **Q5.Write a sql query to find all transaction where total_sale is greater than 1000.**:
```sql
SELECT *
	FROM retail_sales
    WHERE
		total_sale >1000;
```

 **Q6.Write a sql query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
	category,
    gender,
    COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY
 category,gender
ORDER BY 2;
```

 **Q7.Write a sql query to calculate the average sale for each month. Find out best selling month in each year.**:
```sql
SELECT  
    year,
    month,
    avg_sale
FROM 
(
    SELECT   
        YEAR(sale_date) AS year,     
        MONTH(sale_date) AS month,     
        AVG(total_sale) AS avg_sale,     
        RANK() OVER(
					PARTITION BY YEAR(sale_date) 
					ORDER BY AVG(total_sale) DESC
					) AS `rank`
    FROM retail_sales
    GROUP BY 1,2
) AS t1 
WHERE `rank` = 1;
```

 **Q8.Write a sql query to find the top 5 customers based on highest total sales.**:
```sql
SELECT
	customer_id ,
    SUM(total_sale) AS total_sale
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

 **Q9.Write a sql query to find the unique customers who purchased items from each category.**:
```sql
SELECT 
	category,
    COUNT(DISTINCT customer_id) AS unique_customer
FROM retail_sales
GROUP BY 1;
```

 **Q10.Write a sql query to create each shift and number of orders.
       (Example Morning <=12, Afternoon Between 12 & 17, evening >17).**:
```sql
WITH hourly_sale
AS
	(
		SELECT *,
		CASE
			WHEN HOUR(sale_time) <12 THEN "Morning"
			WHEN HOUR(sale_time) BETWEEN 12 AND 17 THEN "Afternoon"
			ELSE "Evening"
		END AS `shift`
		FROM retail_sales
	)
SELECT 
	`shift`,
	COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
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

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles.
