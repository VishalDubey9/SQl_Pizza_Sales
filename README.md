# SQl_Pizza_Sales
![Screenshot 2024-05-13 163710](https://github.com/VishalDubey9/SQl_Pizza_Sales/assets/154626826/d4851890-52c2-41fc-8e20-939cbeb91b3f)

create database Pizza_DB;
Use Pizza_DB;

-- I have created this table  for orders 

create table orders(
order_id int not null,
order_date date not null,
order_time time not null,
primary key(order_id));

-- This table i have created for orders_details table

create table orders_details(
order_details_id int not null,
order_id int not null,
pizza_id text not null,
quantity int not null,
primary key(order_details_id));


select * from orders;
select quantity from orders_details;
select * from  pizza_types;
select * from  pizzas;
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

-- Basic Queary of pizza_sales

-- Retrieve the total number of orders placed

SELECT 
    COUNT(order_id) AS Total_Order
FROM
    orders;


-- Calculate the total revenue generated from pizza sales.

SELECT 
    ROUND(SUM(orders_details.Quantity * Pizzas.Price),
            2) AS Total_Ravanue
FROM
    Pizzas
        JOIN
    orders_details ON orders_details.pizza_id = Pizzas.pizza_id;


-- Identify the highest-priced pizza.

SELECT 
    pizza_types.name, pizzas.price
FROM
    pizzas
        JOIN
    pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;


-- Identify the most common pizza size ordered

SELECT 
    pizzas.size,
    COUNT(orders_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;

-- List the top 5 most ordered pizza types along with their quantities.

SELECT 
    pizza_types.name, SUM(orders_details.quantity) AS Quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.name
ORDER BY Quantity DESC
LIMIT 5
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- Intermediate Queary of pizza_sales

-- Join the necessary tables to find the total quantity of each pizza category ordered 

SELECT 
    pizza_types.category,
    SUM(orders_details.Quantity) AS Total_quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.category
ORDER BY Total_quantity DESC;


-- Determine the distribution of orders by hour of the day

SELECT 
    HOUR(order_time) AS Hour, COUNT(order_id) AS count_order
FROM
    orders
GROUP BY HOUR(order_time);


-- Join relevant tables to find the category-wise distribution of pizzas

SELECT 
    category, COUNT(name) count_pizza
FROM
    pizza_types
GROUP BY category;


-- Group the orders by date and calculate the average number of pizzas ordered per day. 

SELECT 
    ROUND(AVG(Total_order), 0) Avg_Pizza_Orderd_Per_Day
FROM
    (SELECT 
        orders.order_date,
            SUM(orders_details.Quantity) AS Total_order
    FROM
        orders
    JOIN orders_details ON orders.order_id = orders_details.order_id
    GROUP BY order_date) AS Pizza_order_data;
    
    
   --  Determine the top 3 most ordered pizza types based on revenue
   
 SELECT 
    pizza_types.name,
    SUM(orders_details.quantity * pizzas.price) AS Total_Ravanue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.name
ORDER BY Total_Ravanue DESC
LIMIT 3;


-- Calculate the percentage contribution of each pizza type to total revenue

SELECT 
    pizza_types.category,
    Round((SUM(orders_details.quantity * pizzas.price) / (SELECT 
    ROUND(SUM(orders_details.Quantity * Pizzas.Price),
            2) AS Total_Ravanue
FROM
    Pizzas
        JOIN
    orders_details ON orders_details.pizza_id = Pizzas.pizza_id))*100,2) as Total_Ravanue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.category
ORDER BY Total_Ravanue DESC;


-- Analyze the cumulative revenue generated over time.

select order_date,
sum(Revenue) over (order by order_date) as Cum_Rvevenu
from
(select orders.order_date,
sum(orders_details.Quantity*pizzas.price) as Revenue
from orders_details
join pizzas
on orders_details.pizza_id = pizzas.pizza_id
join orders
on orders.order_id  = orders_details.order_id 
group by orders.order_date) as Sales;




