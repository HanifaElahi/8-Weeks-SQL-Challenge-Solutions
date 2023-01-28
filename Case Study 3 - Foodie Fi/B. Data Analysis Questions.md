# 🥑 Case Study 3 - Foodie Fi

# 🎞 B. Data Analysis Questions

<p align="center">
    <a href="#"><img alt="SQL" src="https://custom-icon-badges.demolab.com/badge/SQL-025E8C.svg?logo=database&logoColor=white"></a>
    <a href="#"><img alt="Python" src="https://img.shields.io/badge/Python-FFD43B?style=for-the-badge&logo=python&logoColor=blue"></a>
    <a href="#"><img alt="SQLite" src="https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white"></a>
    <a href="#"><img alt="Jupyter Notebook" src="https://img.shields.io/badge/Jupyter-F37626.svg?&style=for-the-badge&logo=Jupyter&logoColor=white"></a>
</p>

## Solution

***

### 1. How many customers has Foodie-Fi ever had?

````sql

SELECT 
    COUNT(DISTINCT(customer_id)) AS num_customers FROM subscriptions;

````

#### Answer:

| num_customers |
| ------------- | 
|      1000     |  

- Foodie-Fi has 1,000 unique customers.

***

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

````sql

SELECT 
    strftime('%m', start_date) as Month, COUNT(customer_id) AS trial_subscriptions
FROM subscriptions s
INNER JOIN plans p
    ON s.plan_id = p.plan_id
WHERE s.plan_id = 0
GROUP BY Month
ORDER BY Month;

````

#### Answer:

| Month | trial_subscriptions |
|   01  |         88          |
|   02  |         68          |
|   03  |         94          |
|   04  |         81          |
|   05  |         88          |
|   06  |         79          |
|   07  |         89          |
|   08  |         88          |
|   09  |         87          |
|   10  |         79          |
|   11  |         75          |
|   12  |         84          |


- March has the highest number of trial plans, whereas February has the lowest number of trial plans.

***

### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name

````sql

SELECT 
    p.plan_id,
    p.plan_name,
    COUNT(*) AS events
FROM subscriptions s
INNER JOIN plans p
    ON s.plan_id = p.plan_id
WHERE s.start_date >= '2021-01-01'
GROUP BY p.plan_id, p.plan_name
ORDER BY p.plan_id;

````

#### Answer:

| plan_id |   plan_name   | events |
| ------- | ------------- | ------ |
|    1    | basic monthly |    8   |
|    2    |  pro monthly  |   60   |
|    3    |   pro annual  |   63   |
|    4    |      churn    |   71   |

- There were 0 customer on trial plan in 2021. 
- The number of customers who churned the plan is the biggest one after the year 2020.

***

### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

````sql

SELECT 
  COUNT(*) AS churn_count,
  ROUND(100 * COUNT(*)::NUMERIC / (
    SELECT COUNT(DISTINCT customer_id) 
    FROM foodie_fi.subscriptions),1) AS churn_percentage
FROM foodie_fi.subscriptions s
JOIN foodie_fi.plans p
  ON s.plan_id = p.plan_id
WHERE s.plan_id = 4;

````

#### Answer:

| churn_count | churn_percentage |
| --------- | ------------------ |
|    307    |         30         | 

- There are 307 customers who have churned, which is 30.7% of Foodie-Fi customer base.

***

### 5. What was the difference between the longest and shortest delivery times for all orders?

````sql

WITH churn_ranking AS 
(
SELECT 
  s.customer_id, 
  s.plan_id, 
  p.plan_name,
  ROW_NUMBER() OVER (PARTITION BY s.customer_id ORDER BY s.plan_id) AS plan_rank
FROM subscriptions s
JOIN plans p
  ON s.plan_id = p.plan_id
)
  
SELECT 
  COUNT(*) AS churn_count,
  ROUND(100 * COUNT(*) / (
    SELECT COUNT(DISTINCT customer_id) 
    FROM subscriptions),0) AS churn_percentage
FROM churn_ranking
WHERE plan_id = 4
  AND plan_rank = 2;

````

#### Answer:

| churn_count | churn_percentage |
| ----------- | ---------------- |
|      92     |         9       |


- There are 92 customers who have churned straight after their initial free trial, which 9% of the customer base.

***

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

````sql

WITH next_plan AS (
SELECT 
  customer_id, 
  plan_id, 
  LEAD(plan_id, 1) OVER(PARTITION BY customer_id ORDER BY plan_id) as next_plan
FROM subscriptions)

SELECT 
  next_plan, 
  COUNT(*) AS conversions,
  ROUND(100 * COUNT(*) / (
    SELECT COUNT(DISTINCT customer_id) 
    FROM subscriptions),1) AS conversion_percentage
FROM next_plan
WHERE next_plan IS NOT NULL 
  AND plan_id = 0
GROUP BY next_plan
ORDER BY next_plan;

````

#### Answer:

| next_plan | conversions | conversion_percentage | 
| --------- | ----------- | --------------------- | 
|     1     |     546     |           54          |
|     2     |     325     |           37          |
|     3     |     37      |           3           |
|     4     |     92      |           9           |


- 54% of customers choose basic monthly or next_plan = 1 after their initial trial.
- 37% of customers choose pro monthly or next_plan = 2 after their initial trial.
- 3% of customers choose pro annual or next_plan = 3 after their initial trial.
- 9% of customers choose churn or next_plan = 4 after their initial trial.

***

### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?

````sql

WITH 

next_plan AS(
    SELECT 
      customer_id, 
      plan_id, 
      start_date,
      LEAD(start_date, 1) OVER(PARTITION BY customer_id ORDER BY start_date) as next_date
    FROM subscriptions
    WHERE start_date <= '2020-12-31'),

-- To find breakdown of customers with existing plans on or after 31 Dec 2020

customer_breakdown AS (
  SELECT plan_id, COUNT(DISTINCT customer_id) AS customers
    FROM next_plan
    WHERE (next_date IS NOT NULL AND (start_date < '2020-12-31' AND next_date > '2020-12-31'))
      OR (next_date IS NULL AND start_date < '2020-12-31')
    GROUP BY plan_id)

SELECT plan_id, customers, 
  ROUND(100 * customers / (
    SELECT COUNT(DISTINCT customer_id) 
    FROM subscriptions),1) AS percentage
FROM customer_breakdown
GROUP BY plan_id, customers
ORDER BY plan_id

````

#### Answer:

| plan_id | customers | percentage |
| ------- | --------- | ---------- |
|    0    |     19    |     01     |
|    1    |    224    |     22     | 
|    2    |    326    |     32     |
|    3    |    195    |     19     |
|    4    |    235    |     23     |
***

### 8. How many customers have upgraded to an annual in 2020?

````sql

SELECT 
  COUNT(DISTINCT customer_id) AS unique_customer
FROM subscriptions
WHERE plan_id = 3
  AND start_date <= '2020-12-31';

````

#### Answer:

| unique_customer |
| --------------- |
|       195       |

- There’s 195 customers who have upgraded to annual in 2020.

***

### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?

````sql

WITH 
  trial_plan AS 
  (SELECT 
      customer_id, 
      start_date AS trial_date
  FROM subscriptions
  WHERE plan_id = 0
  ),
  
  annual_plan AS
  (SELECT 
      customer_id, 
      start_date AS annual_date
  FROM subscriptions
  WHERE plan_id = 3
  )

SELECT 
    ROUND(AVG(JULIANDAY(ap.annual_date) - JULIANDAY(tp.trial_date))) AS avg_days_to_upgrade
FROM trial_plan tp
INNER JOIN annual_plan ap
    ON tp.customer_id = ap.customer_id

````

#### Answer:

| avg_days_to_upgrade |
| ------------------- |
|         105         |


- On average, it takes 105 days for a customer to upragde to an annual plan from the day they join Foodie-Fi..

***

### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)

````sql

WITH 
  trial_plan AS 
  (SELECT 
      customer_id, 
      start_date AS trial_date
  FROM subscriptions
  WHERE plan_id = 0
  ),
  
  annual_plan AS
  (SELECT 
      customer_id, 
      start_date AS annual_date
  FROM subscriptions
  WHERE plan_id = 3
  ), 

  day_period AS
    (SELECT 
    JULIANDAY(ap.annual_date) - JULIANDAY(tp.trial_date) AS diff
    FROM trial_plan tp
    INNER JOIN annual_plan ap
        ON tp.customer_id = ap.customer_id
    WHERE annual_date IS NOT NULL
    ),
    
  bins AS
  (SELECT 
      *, FLOOR(diff/30) AS bins
    FROM day_period
  )

SELECT ((bins*30)+1) || ' - ' || ((bins+1)*30) || ' days' AS Days,
COUNT(diff) AS Total
FROM bins
GROUP BY bins;


````

#### Answer:

|       Days      | Total |
| --------------- | ----- |
|    1 - 30 days  |   48  |
|   31 - 60 days  |   25  |
|   61 - 90 days  |   33  |
|  91 - 120 days  |   35  |
|  121 - 150 days |   43  |
|  151 - 180 days |   35  |
|  181 - 210 days |   27  |
|  211 - 240 days |   4   | 
|  241 - 270 days |   5   |
|  271 - 300 days |   1   |
|  301 - 330 days |   1   |
|  331 - 360 days |   1   |


***

### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

````sql

SELECT 
  customer_id, 
  plan_id, 
  start_date,
  LEAD(plan_id, 1) OVER(PARTITION BY customer_id ORDER BY plan_id) as next_plan
FROM subscriptions)

SELECT 
  COUNT(*) AS downgraded
FROM next_plan_cte
WHERE start_date <= '2020-12-31'
  AND plan_id = 2 
  AND next_plan = 1;
````

#### Answer:

|  downgraded | 
| ----------- |
|      0      |

- No customer has downgrade from pro monthly to basic monthly in 2020.

***
