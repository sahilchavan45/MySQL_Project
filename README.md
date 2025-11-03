
🍕 SQL Project on Pizza Sales
📋 Overview

This project analyzes pizza sales data using MySQL to uncover valuable business insights.
It demonstrates database querying, joining multiple tables, aggregating data, and performing both basic and advanced SQL analyses.

The goal is to understand sales performance, customer preferences, and revenue distribution across different pizza types, sizes, and categories.

📂 Dataset Information
File Name	Description
orders.csv	Contains details of each order placed (order ID, date, time, etc.)
order_details.csv	Contains order-level details (pizza ID, quantity, etc.)
pizzas.csv	Contains information about pizzas (pizza ID, type, size, price)
pizza_types.csv	Contains pizza categories and descriptions
🧱 Database Schema

Relationships:

orders ↔ order_details — linked by order_id

order_details ↔ pizzas — linked by pizza_id

pizzas ↔ pizza_types — linked by pizza_type_id

Schema Diagram (simplified):

pizza_types (pizza_type_id, name, category, ingredients)
        ↑
        │
pizzas (pizza_id, pizza_type_id, size, price)
        ↑
        │
order_details (order_details_id, order_id, pizza_id, quantity)
        ↑
        │
orders (order_id, date, time)

🧮 Key SQL Queries & Insights
🟢 Basic Queries
1️⃣ Total number of orders placed
SELECT COUNT(order_id) AS total_orders
FROM orders;

2️⃣ Highest-priced pizza
SELECT pizza_id, price
FROM pizzas
ORDER BY price DESC
LIMIT 1;

3️⃣ Most common pizza size ordered
SELECT size, COUNT(size) AS count
FROM pizzas p
JOIN order_details od ON p.pizza_id = od.pizza_id
GROUP BY size
ORDER BY count DESC
LIMIT 1;

4️⃣ Top 5 most ordered pizza types
SELECT pt.name, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_quantity DESC
LIMIT 5;

🟡 Intermediate Queries
5️⃣ Total quantity of each pizza category ordered
SELECT pt.category, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY total_quantity DESC;

6️⃣ Category-wise distribution of pizzas
SELECT category, COUNT(name) AS pizza_count
FROM pizza_types
GROUP BY category;

7️⃣ Average number of pizzas ordered per day
SELECT ROUND(SUM(od.quantity) / COUNT(DISTINCT o.date), 2) AS avg_pizzas_per_day
FROM order_details od
JOIN orders o ON od.order_id = o.order_id;

🔴 Advanced Queries
8️⃣ Top 3 most ordered pizzas by revenue
SELECT pt.name, ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC
LIMIT 3;

9️⃣ Percentage contribution of each pizza type to total revenue
SELECT pt.name,
       ROUND(SUM(od.quantity * p.price) / 
             (SELECT SUM(od.quantity * p.price)
              FROM order_details od
              JOIN pizzas p ON od.pizza_id = p.pizza_id) * 100, 2) AS revenue_percent
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue_percent DESC;

🔟 Cumulative revenue generated over time
SELECT o.date,
       SUM(od.quantity * p.price) AS daily_revenue,
       SUM(SUM(od.quantity * p.price)) OVER (ORDER BY o.date) AS cumulative_revenue
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN pizzas p ON od.pizza_id = p.pizza_id
GROUP BY o.date;

1️⃣1️⃣ Top 3 pizzas by revenue in each category
SELECT category, name, revenue
FROM (
    SELECT pt.category,
           pt.name,
           ROUND(SUM(od.quantity * p.price), 2) AS revenue,
           RANK() OVER (PARTITION BY pt.category ORDER BY SUM(od.quantity * p.price) DESC) AS rnk
    FROM order_details od
    JOIN pizzas p ON od.pizza_id = p.pizza_id
    JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
    GROUP BY pt.category, pt.name
) ranked
WHERE rnk <= 3;

💡 Insights & Findings

Large-sized pizzas are the most frequently ordered.

Classic category pizzas contribute the most to total sales.

A small subset of pizzas contributes to a major share of total revenue (Pareto 80/20 pattern).

Cumulative revenue trend shows consistent growth with periodic peaks.

Thai Chicken Pizza and Barbecue Chicken Pizza are among top performers.

🛠️ Tools & Technologies Used

Database: MySQL

Language: SQL

Data Format: CSV

Visualization (optional): Power BI / Excel

🚀 How to Run the Project

Clone the repository

git clone https://github.com/<your-username>/SQL-Pizza-Sales-Project.git
cd SQL-Pizza-Sales-Project


Create a new MySQL database

CREATE DATABASE pizza_sales;
USE pizza_sales;


Import the datasets

Import CSV files (orders.csv, order_details.csv, pizzas.csv, pizza_types.csv)
using MySQL Workbench or the command line.

Run the SQL queries provided above to analyze the data.

📊 Example Output Metrics
Metric	Value (Example)
Total Orders	21,350
Total Revenue	$817,860
Most Popular Size	Large
Top Pizza Type	Thai Chicken Pizza

(These values are illustrative — run the queries for actual results.)

👨‍💻 Author

Sahil Chavan
"Where every query tells a story."
