# 🍜 Case Study 2: Pizza Runner

# 🍝 A. PIZZA METRICS

<p align="center">
    <a href="#"><img alt="SQL" src="https://custom-icon-badges.demolab.com/badge/SQL-025E8C.svg?logo=database&logoColor=white"></a>
    <a href="#"><img alt="Python" src="https://img.shields.io/badge/Python-FFD43B?style=for-the-badge&logo=python&logoColor=blue"></a>
    <a href="#"><img alt="SQLite" src="https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white"></a>
    <a href="#"><img alt="Jupyter Notebook" src="https://img.shields.io/badge/Jupyter-F37626.svg?&style=for-the-badge&logo=Jupyter&logoColor=white"></a>
</p>

## Solution

***

### 1. How many pizzas were ordered?

````sql

SELECT 
    COUNT((order_id)) AS "No. of Pizza's ordered"
FROM customer_orders;

````

#### Answer:

| No. of Pizza's ordered |
| ---------------------- |
|           14           |


- In total, 14 pizza's were ordered

***

### 2. How many unique customer orders were made?

````sql

SELECT 
    customer_id AS Customer, COUNT(DISTINCT(order_id)) AS "Unique Orders"
FROM customer_orders
GROUP BY Customer;

````

#### Answer:

|  Customer  | Unique Orders |
| ---------- | ------------- |
|    101     |       3       |
|    102     |       2       |
|    103     |       2       |
|    104     |       2       |
|    105     |       1       |

- Customer 101 made 3 unique orders.
- Customer 102 made 2 unique orders.
- Customer 103 made 2 unique orders.
- Customer 104 made 2 unique orders.
- Customer 105 made 1 unique orders.

***

### 3. How many successful orders were delivered by each runner?

````sql

SELECT 
    COUNT(order_id) AS "Successful Orders"
FROM runner_orders
WHERE distance != 0;

````

#### Answer:

| Successful Orders | 
| ----------------- |
|          8        |

- In total, 8 orders were successful

***

### 4. How many of each type of pizza was delivered?

````sql

SELECT 
    c.pizza_id AS "Pizza ID", p.pizza_name AS Pizza, COUNT(c.pizza_id) AS "No.of Delivered Pizza's"
FROM customer_orders AS c
INNER JOIN runner_orders AS r
    ON c.order_id = r.order_id
INNER JOIN pizza_names AS p
    ON c.pizza_id = p.pizza_id
WHERE distance != 0
GROUP BY "Pizza ID";

````

#### Answer:

|  Pizza ID |    Pizza   | No.of Delivered Pizza's |
| --------- | ---------- | ----------------------- |
|     1     | Meatlovers |            9            |
|     2     | Vegetarian |            3            |


- 9 Meatlover pizza's were delivered.
- 3 Vegeterian pizza's were delivered.

***

### 5. How many Vegetarian and Meatlovers were ordered by each customer?

````sql

SELECT 
    c.customer_id AS Customer, p.pizza_name AS Pizza, COUNT(p.pizza_name) AS "No. of Orders"
FROM customer_orders AS c
INNER JOIN pizza_names AS p
    ON c.pizza_id= p.pizza_id
GROUP BY Customer, Pizza
ORDER BY Customer;

````

#### Answer:

| Customer |    Pizza   | No. of Orders |
| -------- | ---------- |-------------- |
|   101    | Meatlovers |       2       |
|   101    | Vegeterian |       1       |
|   102    | Meatlovers |       2       |
|   102    | Vegeterian |       1       |
|   103    | Meatlovers |       3       |
|   103    | Vegeterian |       1       |
|   104    | Meatlovers |       3       |
|   105    | Vegeterian |       1       |

- Customer 101 ordered 2 Meatlovers and 1 Vegeterian Pizza.
- Customer 102 ordered 2 Meatlovers and 1 Vegeterian Pizza.
- Customer 103 ordered 3 Meatlovers and 1 Vegeterian Pizza.
- Customer 104 ordered 3 Meatlovers Pizza.
- Customer 105 ordered 1 Vegeterian Pizza.

***

### 6. What was the maximum number of pizzas delivered in a single order?

````sql

WITH pizza_per_order AS
(
SELECT 
    c.order_id, COUNT(c.pizza_id) AS pizza_per_order
FROM customer_orders AS c
JOIN runner_orders AS r
	ON c.order_id = r.order_id
WHERE r.distance != 0
GROUP BY c.order_id
)

SELECT 
    MAX(pizza_per_order) AS "Pizza Count"
FROM pizza_per_order;

````

#### Answer:

| Pizza Count |
| ----------- | 
|      3      | 

- Maximum number of pizza delivered in a single order is 3.

***

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````sql

SELECT 
  c.customer_id AS Customer,
  SUM(
    CASE WHEN c.exclusions != ' ' OR c.extras != ' ' THEN 1
    ELSE 0
    END) AS at_least_1_change,
  SUM(
    CASE WHEN c.exclusions = ' ' AND c.extras = ' ' THEN 1 
    ELSE 0
    END) AS no_change
    
FROM customer_orders AS c
JOIN runner_orders AS r
  ON c.order_id = r.order_id

WHERE r.distance != 0
GROUP BY Customer
ORDER BY Customer;

````

#### Answer:

| Customer | at_least_1_change  | no_change |
| -------- | ------------------ | --------- |
|    101   |          0         |     0     |
|    102   |          0         |     0     |
|    103   |          3         |     0     |
|    104   |          2         |     0     |
|    105   |          1         |     0     |

- Customer 101 and 102 likes pizzas according to the original recipe.
- Customer 103, 104 and 105 requested at least 1 change (extra or exclusion topping) on their pizza.

***

### 8. How many pizzas were delivered that had both exclusions and extras?

````sql

SELECT  
  SUM(
    CASE WHEN exclusions IS NOT NULL AND extras IS NOT NULL THEN 1
    ELSE 0
    END) AS pizza_with_exclusions_and_extras
FROM customer_orders AS c
JOIN runner_orders AS r
  ON c.order_id = r.order_id
WHERE r.distance >= 1 
  AND exclusions != ' ' 
  AND extras != ' ';

````

#### Answer:

| pizza_with_exclusions_and_extras |
| -------------------------------- |
|                 1                |

- Only 1 delivered pizza had both exclusions and extras.

***

### 9. What was the total volume of pizzas ordered for each hour of the day?

````sql

SELECT 
    strftime('%H', order_time) AS "Hour of day", 
    COUNT(order_id) AS "Pizza Count"
FROM customer_orders
GROUP BY "Hour of day"; 

````

#### Answer:

| Hour of day | Pizza Count | 
| ----------- | ----------- |
|      11     |       1     |
|      13     |       3     |
|      18     |       3     |
|      19     |       1     |
|      21     |       3     |
|      23     |       3     |

- Highest volume of pizza ordered is at 13 (1:00 pm), 18 (6:00 pm) and 21 (9:00 pm).
- Lowest volume of pizza ordered is at 11 (11:00 am), 19 (7:00 pm) and 23 (11:00 pm).

***

### 10. What was the volume of orders for each day of the week?

````sql

SELECT  
    CASE CAST(strftime('%w', order_time) as integer)
        WHEN 0 THEN 'Sunday'
        WHEN 1 THEN 'Monday'
        WHEN 2 THEN 'Tuesday'
        WHEN 3 THEN 'Wednesday'
        WHEN 4 THEN 'Thursday'
        WHEN 5 THEN 'Friday'
        ELSE 'Saturday' 
    END AS "Week Day",
    COUNT(order_id) AS "Total Pizzas"
FROM customer_orders
GROUP BY "Week Day"
ORDER BY "Total Pizzas" DESC

````

#### Answer:

|  Week Day | Total Pizzas | 
| --------- | ------------ |
| Wednesday |       5      |
| Saturday  |       5      |
| Thursday  |       3      |
| Friday    |       1      |

- 5 pizzas are ordered on Wednesday and Saturday.
- 3 pizzas are ordered on Thursday.
- 1 pizza is ordered on Friday.

***

