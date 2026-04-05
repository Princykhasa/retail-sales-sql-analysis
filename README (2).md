# 🛒 Retail Sales Analysis — SQL Project

![SQL](https://img.shields.io/badge/Language-SQL-blue?style=for-the-badge&logo=postgresql)
![Database](https://img.shields.io/badge/Database-PostgreSQL-336791?style=for-the-badge&logo=postgresql)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Beginner--Intermediate-orange?style=for-the-badge)

---

## 📖 Project Overview

**Project Title:** Retail Sales Analysis  
**Level:** Beginner – Intermediate  
**Tool Used:** PostgreSQL  
**Database Name:** `sql_project_p2`

This project is a complete, end-to-end SQL analysis built on a retail sales dataset. The goal is to simulate the real workflow of a data analyst — starting from setting up a raw database, cleaning messy data, exploring the dataset, and finally answering important business questions using SQL queries.

Whether you are new to SQL or brushing up your skills, this project walks through every step with clear explanations. By the end, you will understand how SQL can be used to uncover patterns in sales data, understand customer behaviour, and support business decisions.

---

## 🎯 Objectives

The project is built around four core objectives:

1. **Set up the database and table structure** — design a proper schema and load the retail sales data into it.
2. **Clean the data** — identify and remove records that are incomplete or contain NULL values.
3. **Explore the data** — run basic queries to understand the size and shape of the dataset.
4. **Answer business questions** — write targeted SQL queries that produce real, actionable insights.

---

## 🗂️ Table of Contents

- [Database Setup](#-phase-1-database-setup)
- [Data Cleaning](#-phase-2-data-cleaning)
- [Data Exploration](#-phase-3-data-exploration-eda)
- [Business Questions & SQL Queries](#-phase-4-business-questions--sql-queries)
- [Key Findings](#-key-findings)
- [How to Run This Project](#-how-to-run-this-project)
- [Project File Structure](#-project-file-structure)

---

## 🏗️ Phase 1: Database Setup

The first step is creating the database and the table that will hold all the sales data.

A database called `sql_project_p2` is created, and inside it a table named `retail_sales` is defined. Each column in the table represents one piece of information about a sales transaction — things like the date and time of sale, who bought it, what they bought, how many units, and how much was paid.

```sql
CREATE DATABASE sql_project_p2;

CREATE TABLE retail_sales (
    transactions_id   INT PRIMARY KEY,
    sale_date         DATE,
    sale_time         TIME,
    customer_id       INT,
    gender            VARCHAR(15),
    age               INT,
    category          VARCHAR(15),
    quantity          INT,
    price_per_unit    FLOAT,
    cogs              FLOAT,
    total_sale        FLOAT
);
```

### 📋 Column Reference

| Column Name       | Type         | Description                                              |
|-------------------|--------------|----------------------------------------------------------|
| `transactions_id` | INT (PK)     | A unique number identifying each sale transaction        |
| `sale_date`       | DATE         | The calendar date on which the sale happened             |
| `sale_time`       | TIME         | The exact time of the sale (used for shift analysis)     |
| `customer_id`     | INT          | A number identifying the customer who made the purchase  |
| `gender`          | VARCHAR(15)  | Gender of the customer (Male / Female)                   |
| `age`             | INT          | Age of the customer at the time of purchase              |
| `category`        | VARCHAR(15)  | The product category (e.g., Clothing, Beauty, Electronics) |
| `quantity`        | INT          | Number of units of the product purchased                 |
| `price_per_unit`  | FLOAT        | Selling price of one unit of the product                 |
| `cogs`            | FLOAT        | Cost of Goods Sold — what it cost the business to stock it |
| `total_sale`      | FLOAT        | Total revenue from the transaction (quantity × price)    |

---

## 🧹 Phase 2: Data Cleaning

Raw data is never perfect. Before doing any analysis, it is important to find and fix data quality issues — otherwise the results will be unreliable.

### Step 1 — Find the Problem Rows

This query scans every column for `NULL` values. A `NULL` means the field is empty or missing. Any row where even one column is NULL is considered an incomplete record.

```sql
SELECT * FROM retail_sales
WHERE
    transactions_id  IS NULL OR
    sale_date        IS NULL OR
    sale_time        IS NULL OR
    customer_id      IS NULL OR
    gender           IS NULL OR
    age              IS NULL OR
    category         IS NULL OR
    quantity         IS NULL OR
    price_per_unit   IS NULL OR
    cogs             IS NULL OR
    total_sale       IS NULL;
```

> **Why this matters:** If a row is missing `total_sale` or `quantity`, it cannot contribute to any meaningful calculation. Keeping it would silently skew results.

### Step 2 — Remove the Problem Rows

Once identified, those incomplete rows are permanently deleted from the table.

```sql
DELETE FROM retail_sales
WHERE
    transactions_id  IS NULL OR
    sale_date        IS NULL OR
    sale_time        IS NULL OR
    customer_id      IS NULL OR
    gender           IS NULL OR
    age              IS NULL OR
    category         IS NULL OR
    quantity         IS NULL OR
    price_per_unit   IS NULL OR
    cogs             IS NULL OR
    total_sale       IS NULL;
```

After this step, the dataset is clean and ready for analysis.

---

## 🔍 Phase 3: Data Exploration (EDA)

Before jumping into complex queries, it helps to get a feel for the dataset — how large is it, who is in it, and what categories exist.

### How many total sales transactions are there?
```sql
SELECT COUNT(*) AS total_sale FROM retail_sales;
```
> Gives us the total number of rows (i.e., transactions) in the cleaned dataset.

### How many unique customers made purchases?
```sql
SELECT COUNT(DISTINCT customer_id) AS total_customers FROM retail_sales;
```
> Helps understand the customer base size — are we dealing with 50 customers or 5,000?

### What product categories exist in the data?
```sql
SELECT DISTINCT category FROM retail_sales;
```
> Lists all the different product types in the store (e.g., Clothing, Beauty, Electronics).

---

## 📊 Phase 4: Business Questions & SQL Queries

This is the core analytical section. Ten business questions are answered using SQL — each one designed to reveal something useful about the retail operation.

---

### 🔎 Q1. What sales were made on a specific date?

**Business Context:** A manager wants to review all transactions from a particular day — for example, to investigate a reported spike or drop in sales.

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**Explanation:** Filters all records to show only those made on November 5, 2022. Every column is returned so the full picture of each transaction on that day is visible.

---

### 🔎 Q2. Which Clothing transactions had high quantity in November 2022?

**Business Context:** The inventory team wants to know when large clothing purchases happened in a specific month — useful for understanding bulk-buying behaviour or unusual demand.

```sql
SELECT *
FROM retail_sales
WHERE
    category = 'Clothing'
    AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND quantity >= 4;
```

**Explanation:** `TO_CHAR()` converts the date into a `YYYY-MM` formatted string so we can filter by month without needing the exact day. Only Clothing transactions with 4 or more units sold are returned.

---

### 🔎 Q3. What is the total revenue generated by each product category?

**Business Context:** Which category brings in the most money? This helps decide where to invest more stock or marketing budget.

```sql
SELECT
    category,
    SUM(total_sale)  AS net_sale,
    COUNT(*)         AS total_orders
FROM retail_sales
GROUP BY category;
```

**Explanation:** `SUM(total_sale)` adds up all revenue per category. `COUNT(*)` shows how many individual orders each category had — so we can tell whether high revenue comes from high volume or high-value items (or both).

---

### 🔎 Q4. What is the average age of customers buying Beauty products?

**Business Context:** The marketing team wants to understand the typical age of their Beauty customers so they can design targeted campaigns for the right demographic.

```sql
SELECT
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

**Explanation:** Filters only Beauty purchases, then calculates the mean age of those customers. `ROUND(..., 2)` keeps the result clean by limiting it to two decimal places.

---

### 🔎 Q5. Which transactions had a total sale value above 1000?

**Business Context:** Identifying high-value purchases helps the business spot premium customers and high-margin transactions worth tracking for loyalty programmes or upselling.

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

**Explanation:** A simple threshold filter. Every transaction where the customer spent more than 1000 is returned with its full details, making it easy to review and act on.

---

### 🔎 Q6. How many transactions did each gender make in each category?

**Business Context:** Understanding gender-based purchasing across categories helps tailor product placement, store layouts, and targeted promotions.

```sql
SELECT
    category,
    gender,
    COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

**Explanation:** Groups data by both `category` and `gender` together, producing a breakdown such as: Clothing → Male: 120, Clothing → Female: 145, Beauty → Male: 60, and so on. Ordering by category makes the output easy to read.

---

### 🔎 Q7. Which month in each year had the highest average sales?

**Business Context:** Identifying peak sales months allows the business to plan stock levels, staffing, and promotional campaigns well in advance.

```sql
SELECT year, month, avg_sale
FROM (
    SELECT
        EXTRACT(YEAR  FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale)               AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS t1
WHERE rank = 1;
```

**Explanation:** The inner query calculates average sales per month for each year, then uses `RANK()` — a window function — to rank every month within its year from highest average to lowest. The outer query keeps only `rank = 1`, which gives us the single best-performing month for each year.

---

### 🔎 Q8. Who are the top 5 customers by total spending?

**Business Context:** High-spending customers are extremely valuable. Identifying them enables loyalty rewards, personalised offers, and targeted retention strategies.

```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

**Explanation:** Groups all transactions by customer and sums their total spending across all visits. Sorted from highest to lowest, `LIMIT 5` then keeps only the five biggest spenders.

---

### 🔎 Q9. How many unique customers bought from each category?

**Business Context:** A category with high revenue but few unique customers may be driven by a small group of repeat buyers — this query exposes that distinction.

```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

**Explanation:** `COUNT(DISTINCT customer_id)` ensures each customer is counted only once per category — even if they purchased from that category multiple times. This gives us the true reach of each category.

---

### 🔎 Q10. How many orders were placed during each shift of the day?

**Business Context:** Knowing when customers shop most — morning, afternoon, or evening — helps the store schedule staff efficiently and plan flash sales at peak times.

```sql
WITH hourly_sales AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12               THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17  THEN 'Afternoon'
            ELSE                                                       'Evening'
        END AS shift
    FROM retail_sales
)
SELECT
    shift,
    COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift;
```

**Explanation:** A CTE (Common Table Expression) named `hourly_sales` is built first — it adds a new column `shift` to every row by extracting the hour from `sale_time` and classifying it. The main query then simply counts how many orders fall into each shift. Using a CTE keeps the logic organised and easy to follow.

| Shift     | Time Range            |
|-----------|-----------------------|
| Morning   | Before 12:00 PM       |
| Afternoon | 12:00 PM – 5:00 PM    |
| Evening   | After 5:00 PM         |

---

## 💡 Key Findings

After running all queries, the following insights emerged from the data:

- **Category Revenue:** Certain categories generate more total revenue while others have more individual orders — understanding this difference guides both stock and pricing strategy.
- **Peak Sales Months:** Monthly analysis reveals seasonal patterns, with some months significantly outperforming others. These peaks are ideal windows for running promotions.
- **Beauty Customer Profile:** Beauty category buyers fall within a specific average age range, making demographic targeting more precise and effective.
- **High-Value Transactions:** A portion of transactions exceed 1000 in total sale value, pointing to a segment of premium buyers worth nurturing through loyalty programmes.
- **Shopping Shift Patterns:** Orders are not evenly spread across the day — knowing which shift is busiest helps the store optimise staffing and plan time-sensitive offers.
- **Gender Purchasing Behaviour:** Transaction counts broken down by gender and category reveal clear differences in what each group buys, which can inform product placement and marketing.
- **Top Customer Concentration:** A small group of customers accounts for a disproportionately large share of total revenue — a classic pattern that makes customer retention critically important.

---

## ▶️ How to Run This Project

Follow these steps to set up and run the project on your own machine:

**Step 1 — Install PostgreSQL**  
Download and install PostgreSQL from [postgresql.org](https://www.postgresql.org/download/)

**Step 2 — Clone this repository**
```bash
git clone https://github.com/your-username/retail-sales-sql.git
cd retail-sales-sql
```

**Step 3 — Create the database**
```sql
CREATE DATABASE sql_project_p2;
```

**Step 4 — Run the SQL file**  
Open `Retail_Sales_Query.sql` in pgAdmin, or run it via the terminal:
```bash
psql -U your_username -d sql_project_p2 -f Retail_Sales_Query.sql
```

**Step 5 — Explore**  
Once the data is loaded, execute the queries section by section and observe the results.

---

## 📁 Project File Structure

```
retail-sales-sql/
│
├── Retail_Sales_Query.sql    # All SQL: database setup, data cleaning, EDA, and analysis
└── README.md                 # Full project documentation (this file)
```

---

## 👤 Author

**PRINCY**  
Aspiring Data Analyst | SQL • Data Exploration • Business Insights  
📧princykhasa5@gmail.com  
🔗 [LinkedIn]--(https://www.linkedin.com/in/princykhasa)

> ⭐ *If this project helped you learn or gave you ideas, consider starring the repository — it helps others find it too!*
