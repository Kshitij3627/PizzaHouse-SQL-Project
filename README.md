# PizzaHouse-SQL-Project

# Project Overview

This project analyzes a Pizza Sales dataset to
understand sales performance and customer
preferences.  It includes creating and managing
tables, performing CRUD operations, and executing
advanced SQL queries. The goal is to showcase skills
in database design, manipulation, and querying.

# Dataset Summary

- A comprehensive dataset capturing pizza orders,
 product details, and daily sales metrics.
 
- Useful for identifying best-selling items, sales
 trends, and performance patterns.
 
- Supports actionable insights for marketing,
 inventory planning, and business growth.

Task 1: Retrieve the total number of orders placed.
```sql
SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
```
Task 2: Calculate the total revenue generated from pizza sales.
```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    order_details
        JOIN
    pizzas ON order_details.pizza_id = pizzas.pizza_id;
```
Task 3: Identify the highest-priced pizza.
```sql
SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
ORDER BY price DESC
LIMIT 1;
```
Task 4: Identify the most common pizza size ordered.
```sql
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC
LIMIT 1;
```
Task 5: List the top 5 most ordered pizza types along with their quantities.
```sql
SELECT 
    pizza_types.name, SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC LIMIT 5;
```
Task 6: Join the necessary tables to find the total quantity of each pizza category ordered.
```sql
SELECT 
    pizza_types.category,
    SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity;
```
Task 7: Determine the distribution of orders by hour of the day.
```sql
SELECT 
    HOUR(order_time) AS hour, COUNT(order_id) AS orders
FROM
    orders
GROUP BY HOUR(order_time);
```
Task 8: Join relevant tables to find the category-wise distribution of pizzas.
```sql
SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;
```
Task 9: Group the orders by date and calculate the average number of pizzas ordered per day.
```sql
SELECT 
    ROUND(AVG(quantity), 0) AS avg_pizzas_ordered
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS data;
```
Task 10: Determine the top 3 most ordered pizza types based on revenue.
```sql
SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```
Task 11: Calculate the percentage contribution of each pizza type to total revenue.
```sql
SELECT 
    pizza_types.category,
    ROUND(SUM(order_details.quantity * pizzas.price) / (SELECT 
                    ROUND(SUM(order_details.quantity * pizzas.price),
                                2) AS total_sales
                FROM
                    order_details
                        JOIN
                    pizzas ON order_details.pizza_id = pizzas.pizza_id) * 100,
            2) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```
Task 12: Analyze the cumulative revenue generated over time.
```sql
SELECT 
     order_date,
	SUM(revenue) OVER(ORDER BY order_date) AS cum_revenue
FROM
  (SELECT 
     orders.order_date,
     SUM(order_details.quantity * pizzas.price) AS revenue 
FROM 
     order_details JOIN pizzas
ON order_details.pizza_id = pizzas.pizza_id
JOIN orders
ON orders.order_id = order_details.order_id
GROUP BY orders.order_date) AS sales;
```
Task 13: Determine the top 3 most ordered pizza types based on revenue for each pizza category.
```sql
SELECT 
	category, name, revenue
FROM
    (SELECT category, name, revenue,
     RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
FROM
     (SELECT pizza_types.category,
	 pizza_types.name,
     SUM(order_details.quantity * pizzas.price) AS revenue
FROM 
   pizza_types JOIN pizzas
ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details ON
order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category, pizza_types.name) AS a) AS b
WHERE rn <=3;
```
