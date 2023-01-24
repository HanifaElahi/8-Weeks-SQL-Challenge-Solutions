# 🍜 Case Study 2: Pizza Runner

# 🍝 B. Runner and Customer Experience

<p align="center">
    <a href="#"><img alt="SQL" src="https://custom-icon-badges.demolab.com/badge/SQL-025E8C.svg?logo=database&logoColor=white"></a>
    <a href="#"><img alt="Python" src="https://img.shields.io/badge/Python-FFD43B?style=for-the-badge&logo=python&logoColor=blue"></a>
    <a href="#"><img alt="SQLite" src="https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white"></a>
    <a href="#"><img alt="Jupyter Notebook" src="https://img.shields.io/badge/Jupyter-F37626.svg?&style=for-the-badge&logo=Jupyter&logoColor=white"></a>
</p>

## Solution

***

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

````sql

SELECT 
  (cast(strftime('%d', registration_date) as int) / 7) + 1 AS "Registration Week",
  COUNT(runner_id) AS "Runner Signup"
FROM runners
GROUP BY "Registration Week";

````

#### Answer:

| Registration Week | Runner Signup |
| ----------------- | ------------- |
|         1         |       2       |        
|         2         |       1       |     
|         3         |       1       | 

- On Week 1 of Jan 2021, 2 runners signed up.
- On Week 2 and 3 of Jan 2021, 1 runner signed up.

***

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql

WITH time_taken_cte AS
(
  SELECT 
    c.order_id, 
    c.order_time, 
    r.pickup_time, 
    ROUND((JULIANDAY(r.pickup_time) - JULIANDAY(c.order_time))* 1440) AS pickup_minutes
  FROM customer_orders AS c
  JOIN runner_orders AS r
    ON c.order_id = r.order_id
  WHERE r.distance != 0
  GROUP BY c.order_id, c.order_time, r.pickup_time
)

SELECT 
  ROUND(AVG(pickup_minutes)) AS "Avg Pickup Mins"
FROM time_taken_cte
WHERE pickup_minutes > 1;

````

#### Answer:

| Avg Pickup Mins |
|        16       |

- The average time taken in minutes by runners to arrive at Pizza Runner HQ to pick up the order is 16 minutes.

***

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

````sql

WITH prep_time AS
(
    SELECT 
        c.order_id, 
        COUNT(c.order_id) AS pizza_order, 
        c.order_time, 
        r.pickup_time, 
        ROUND(((JULIANDAY(r.pickup_time) - JULIANDAY(c.order_time))* 1440)) AS prep_minutes
    FROM customer_orders AS c
    INNER JOIN runner_orders AS r
        ON c.order_id = r.order_id
    WHERE r.distance != 0
    GROUP BY c.order_id, c.order_time, r.pickup_time
)

SELECT 
    pizza_order AS "Pizza Order", 
    AVG(prep_minutes) AS "Avg Prep Time (Mins)"
FROM prep_time
WHERE prep_minutes > 1
GROUP BY "Pizza Order";

````

#### Answer:

| Pizza Order | Avg Prep Time (Mins) |
| ----------- | -------------------- |
|      1      |         12.2         |
|      2      |         18.5         |
|      3      |         29.0         |

- On average, a single pizza order takes 12 minutes to prepare.
- An order with 3 pizzas takes 29 minutes at an average of around 10 minutes per pizza.
- It takes 18 minutes to prepare an order with 2 pizzas which is 9 minutes per pizza.

***

### 4. What was the average distance travelled for each customer?

````sql

SELECT 
  c.customer_id AS Customer, 
  ROUND(AVG(r.distance)) AS "Avg Distance"
FROM customer_orders AS c
JOIN runner_orders AS r
  ON c.order_id = r.order_id
WHERE r.duration != 0
GROUP BY Customer;

````

#### Answer:

|  Customer | Avg Distance |
| --------- | ------------ |
|    101    |      20      | 
|    102    |      17      |
|    103    |      23      |
|    104    |      10      |
|    105    |      25      |

- For customer 101, average distance travelled was 20km.
- For customer 102, average distance travelled was 17km.
- For customer 103, average distance travelled was 23km.
- For customer 104, average distance travelled was 10km.
- For customer 105, average distance travelled was 25km.

***

### 5. What was the difference between the longest and shortest delivery times for all orders?

````sql

SELECT 
    MAX(duration) - MIN(duration) AS "Delivery Time Difference"
FROM runner_orders
WHERE duration NOT LIKE ' ';

````

#### Answer:

|  	Delivery Time Difference |
| -------------------------- |
|            30              |


- The difference between longest (40 minutes) and shortest (10 minutes) delivery time for all orders is 30 minutes.

***

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql

SELECT 
  r.runner_id AS "Runner ID", 
  c.customer_id AS "Customer ID", 
  c.order_id AS "Order ID", 
  COUNT(c.order_id) AS "Pizza Count", 
  r.distance AS Distance, 
  (r.duration / 60) AS "Duration Hr" , 
  ROUND((r.distance/r.duration * 60), 2) AS "Avg speed"
FROM runner_orders AS r
JOIN customer_orders AS c
  ON r.order_id = c.order_id
WHERE distance != 0
GROUP BY "Runner ID", "Customer ID", "Order ID", Distance, r.duration
ORDER BY "Customer ID";

````

#### Answer:

| Runner ID | Customer ID | Order ID | Pizza Count | Distance | Duration Hr | Avg speed | 
| --------- | ----------- | -------- | ----------- | -------- | ----------- | --------- |
|     1     |     101     |     1    |      1      |     20   |     0.53    |    37.5   |
|     1     |     101     |     2    |      1      |     20   |     0.45    |    44.4   |
|     1     |     102     |     3    |      2      |   13.4   |     0.33    |    40.2   |
|     2     |     102     |     8    |      1      |   23.4   |     0.22    |    93.6   |
|     2     |     103     |     4    |      3      |   23.4   |     0.67    |    35.1   |
|     1     |     104     |    10    |      2      |     10   |     0.16    |    60.0   |
|     3     |     104     |     5    |      1      |     10   |     0.25    |    40.0   |
|     2     |     105     |     7    |      1      |     25   |     0.42    |    60.0   |

- Runner 1’s average speed is from 37.5km/h to 60km/h.
- Runner 2’s average speed is from 35.1km/h to 93.6km/h.
- Runner 3’s average speed is 40km/h

***

### 7. What is the successful delivery percentage for each runner?

````sql

SELECT 
    runner_id AS Runner, 
    ROUND(100 * SUM(
        CASE WHEN distance != 0 THEN 1
        ELSE 0 END) / COUNT(*), 0) AS "Success Percentage"
FROM runner_orders
GROUP BY runner_id;

````

#### Answer:

| Runner | Success Percentage |
| ------ | ------------------ |
|    1   |        100         |
|    2   |         75         | 
|    3   |         50         |


- Runner 1 has 100% successful delivery percentage.
- Runner 2 has 75% successful delivery percentage.
- Runner 3 has 50% successful delivery percentage.

***

