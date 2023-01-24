# 🍜 Case Study 2: Pizza Runner

# 🍝 C. Ingredient Optimisation

<p align="center">
    <a href="#"><img alt="SQL" src="https://custom-icon-badges.demolab.com/badge/SQL-025E8C.svg?logo=database&logoColor=white"></a>
    <a href="#"><img alt="Python" src="https://img.shields.io/badge/Python-FFD43B?style=for-the-badge&logo=python&logoColor=blue"></a>
    <a href="#"><img alt="SQLite" src="https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white"></a>
    <a href="#"><img alt="Jupyter Notebook" src="https://img.shields.io/badge/Jupyter-F37626.svg?&style=for-the-badge&logo=Jupyter&logoColor=white"></a>
</p>

## Solution

***

### 1. What are the standard ingredients for each pizza?

````sql

WITH RECURSIVE split(pizza_id, topping_id, rest) AS (
   SELECT pizza_id, '', toppings||',' FROM pizza_recipes
   UNION ALL SELECT
   pizza_id,
   substr(rest, 0, instr(rest, ',')),
   substr(rest, instr(rest, ',')+1)
   FROM split WHERE rest!=''
   ORDER BY pizza_id
)

SELECT 
    pn.pizza_name AS Pizza , pt.topping_name AS Ingredient
FROM split AS s
INNER JOIN pizza_names AS pn
    ON s.pizza_id = pn.pizza_id
INNER JOIN pizza_toppings AS pt
    ON s.topping_id = pt.topping_id;

````

#### Answer:

|    Pizza   | Ingredient |
| ---------- | ---------- |
| Meatlovers |   Salami   |   
| Meatlovers |  BBQ Sauce |  
| Meatlovers |    Beef    |  
| Meatlovers |   Cheese   |       
| Meatlovers |   Chicken  |  
| Meatlovers | Mushrooms  |  
| Meatlovers |  Pepperoni |  
| Meatlovers |    Bacon   |  
| Vegetarian |  Tomatoes  |     
| Vegetarian |Tomato Sauce|
| Vegetarian |  Mushrooms |
| Vegetarian |   Onions   |
| Vegetarian |  Peppers   |
| Vegetarian |   Cheese   |

- For Meatlovers pizza, standard ingredients are Salami, BBQ Sauce, Beef, Cheese, chicken, Mushrooms, Pepperoni, & Bacon.
- For Vegetarian pizza, standard ingredients are Tomatoes, Tomato Sauce, Mushrooms, Onions, Peppers & Cheese.

***

### 2. What was the most commonly added extra?

````sql

WITH RECURSIVE extra_split(order_id, customer_id ,pizza_id, extras, rest) AS (
    SELECT order_id, customer_id ,pizza_id, '', extras||',' FROM customer_orders
    UNION ALL SELECT
    order_id, customer_id ,pizza_id,
    substr(rest, 0, instr(rest, ',')),
    substr(rest, instr(rest, ',')+1)
    FROM extra_split WHERE rest!=''
    ORDER BY order_id, customer_id ,pizza_id
    )

SELECT 
    topping_name AS Extra, COUNT(extras) AS "Times ordered"
FROM extra_split AS es
INNER JOIN pizza_toppings AS pr
    ON es.extras = pr.topping_id
GROUP BY topping_name
ORDER BY "Times Ordered" DESC
LIMIT 1;


````

#### Answer:

|  Extra  | Times Ordered |
| ------- | ------------- |
|  Bacon  |        4      |


- The most commonly added extra was Bacon.

***

### 3. What was the most common exclusion?

````sql

WITH RECURSIVE exclusion_split(order_id, customer_id ,pizza_id, exclusion, rest) AS (
    SELECT order_id, customer_id ,pizza_id, '', exclusions||',' FROM customer_orders
    UNION ALL SELECT
    order_id, customer_id ,pizza_id,
    substr(rest, 0, instr(rest, ',')),
    substr(rest, instr(rest, ',')+1)
    FROM exclusion_split WHERE rest!=''
    ORDER BY order_id, customer_id ,pizza_id
)

SELECT 
    topping_name AS Exclusion, COUNT(exclusion) AS "Times ordered"
FROM exclusion_split AS es
INNER JOIN pizza_toppings AS pr
    ON es.exclusion = pr.topping_id
GROUP BY topping_name
ORDER BY "Times Ordered" DESC
LIMIT 1;

````

#### Answer:

| Exclusion | Times Ordered |
| --------- | ------------- |
|   Cheese  |        4      |


- The most commonly added exclusion was Cheese.

***

