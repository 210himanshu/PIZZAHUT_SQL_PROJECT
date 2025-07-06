# PIZZAHUT SLAES DATA ANALYSIS USING SQL
![Pizzahut logo](https://github.com/210himanshu/PIZZAHUT_SQL_PROJECT/blob/main/pizza%20hut%20logo.jpeg)
## 📌 Overview

In today’s data-driven environment, businesses like PizzaHut need actionable insights from their data to stay competitive. This project uses structured SQL queries to explore and analyze pizza sales data, providing key business metrics such as revenue trends, top customers, and most popular items.

The project uses **MySQL** as the primary tool for querying and analysis. Queries are structured and optimized to reflect real-world business questions that can support data-driven decisions in a retail/food chain environment.

---

## 🎯 Objective
The objective of this project is to analyze PizzaHut’s sales data using SQL to extract meaningful business insights. The analysis covers key metrics such as total orders, total revenue, most ordered and highest-priced pizzas, popular sizes, and customer preferences. It also explores trends like order distribution by time, daily averages, revenue contribution by pizza types, and category-wise performance. The goal is to demonstrate the use of SQL for real-world data analysis and support data-driven decision-making.
The ultimate goal is to **transform raw data into meaningful insights** for decision-making.

---

### 1. Retrieve the total number of orders placed.
```sql
select count(order_id) as total_orders from orders;
```
### 2. Calculate the total revenue generated from pizza sales.
```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    order_details
        JOIN
    pizzas ON pizzas.pizza_id = order_details.pizza_id
``` 
### 3. Identify the highest-priced pizza.
```sql
SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```
### 4. Identify the most common pizza size ordered.
```sql
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
``` 
### 5. List the top 5 most ordered pizza types along with their quantities.
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
ORDER BY quantity DESC
LIMIT 5;
``` 
### 6. Determine the distribution of orders by hour of the day.
```sql
SELECT 
    HOUR(order_time) AS hour, COUNT(order_id) AS order_count
FROM
    orders
GROUP BY HOUR(order_time);
``` 
### 7. Join relevant tables to find the category-wise distribution of pizzas.
```sql
SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;
``` 
### 8. Group the orders by date and calculate the average number of pizzas ordered per day.
```sql
SELECT 
    AVG(quantity) as avg_pizza_ordered_per_day
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
``` 
### 9. Determine the top 3 most ordered pizza types based on revenue.
```sql
select pizza_types.name,
sum(order_details.quantity * pizzas.price) as revenue 
from pizza_types join pizzas
on pizzas.pizza_type_id = pizza_types.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.name order by revenue desc limit 3;
```
### 10. Calculate the percentage contribution of each pizza type to total revenue.
```sql
SELECT 
    pizza_types.category,
    (SUM(order_details.quantity * pizzas.price) / (SELECT 
            ROUND(SUM(order_details.quantity * pizzas.price),
                        2) AS total_sales
        FROM
            order_details
                JOIN
            pizzas ON pizzas.pizza_id = order_details.pizza_id)) * 100 AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;    
```
### 11. Analyze the cumulative revenue generated over time.
```sql
select order_date,
sum(revenue) over(order by order_date) as cum_revenue
from 
(select orders.order_date,
sum(order_details.quantity * pizzas.price) as revenue
from order_details join pizzas
on order_details.pizza_id = pizzas.pizza_id
join orders
on orders.order_id = order_details.order_id
group by orders.order_date) as sales;
```
### 12. Determine the top 3 most ordered pizza types based on revenue for each pizza category.
```sql
select name, revenue from
(select category, name, revenue,
rank() over(partition by category order by revenue desc) as rn
from
(select pizza_types.category, pizza_types.name,
sum((order_details.quantity) * pizzas.price) as revenue
from pizza_types join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category, pizza_types.name) as a) as b
where rn <= 3;
```

