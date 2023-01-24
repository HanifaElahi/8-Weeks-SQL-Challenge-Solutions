# 🍕 Case Study 2 - Pizza Runner

## 🧼 Data Cleaning

### 🔨 Table: runners

**No cleaning required**

### 🔨 Table: pizza_names

**No cleaning required**

### 🔨 Table: pizza_recipes

**No cleaning required**

### 🔨 Table: pizza_toppings

**No cleaning required**

### 🔨 Table: customer_orders

- `exclusions` column :  blank space and null values. 
- `extras` column : blank space and null values.

Course of action:
- Removed null values in `exlusions` and `extras` columns and replaced them with blank space ' '.

````sql
UPDATE customer_orders 
SET 
    exclusions = '',
    extras = ''
    WHERE (
        exclusions IS NULL OR exclusions LIKE 'null' OR extras IS NULL OR extras LIKE 'null'
        );
`````
***

### 🔨 Table: runner_orders

- `pickup_time` column :  blank space and null values. 
- `distance` column : km and null values. 
- `duration` column : mins, minute, minutes and null values.
- `cancellation` column : blank space and null values.

Course of action:

- `pickup_time` column : removed null and replaced it with blank space.
- `distance` column : removed "km" and null , and replaced them with blank space.
- `duration` column : removed "mins", "minutes", "minute" and null, and replaced them with blank space.
- `cancellation` column : removed null, and replaced it with blank space.

````sql
UPDATE runner_orders 
SET 
    pickup_time =
    (CASE
        WHEN pickup_time IS NULL OR pickup_time LIKE 'null' THEN ' '
        ELSE pickup_time
     END),
    distance = 
    (CASE
        WHEN distance IS NULL OR distance LIKE 'null' THEN ' '
        WHEN distance LIKE '%km' THEN TRIM('km', distance)
        ELSE distance 
    END),
    duration = 
    (CASE 
        WHEN duration IS NULL OR duration LIKE 'null' THEN ' '
        WHEN duration LIKE '%mins' THEN TRIM('mins', duration)
        WHEN duration LIKE '%minute' THEN TRIM('minute', duration)
        WHEN duration LIKE '%minutes' THEN TRIM('minutes', duration)
        ELSE duration
    END),
    cancellation = 
    (CASE
       WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ' '
       ELSE cancellation
    END);
````

***
