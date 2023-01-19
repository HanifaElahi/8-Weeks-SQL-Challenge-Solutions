# 🍜 Case Study 1: Danny's Diner

<p>
    <a href="#"><img alt="SQL" src="https://img.shields.io/badge/SQL-018bff.svg?&style=for-the-badge&logo=sql&logoColor=white"></a>
    <a href="#"><img alt="Python" src="https://img.shields.io/badge/Python-F02E65?style=for-the-badge&logo=Python&logoColor=white"></a>
    <a href="#"><img alt="SQLite" src="https://img.shields.io/badge/SQLite-311C87?style=for-the-badge&logo=sqlite&logoColor=white"></a>
    <a href="#"><img alt="Jupyter" src="https://img.shields.io/badge/Jupyter-FF6600.svg?&style=for-the-badge&logo=Jupyter&logoColor=white"></a>
</p>

## Solution

- [Dataset Directory](https://github.com/HanifaElahi/8-Weeks-SQL-Challenge-Solutions/tree/main/Case%20Study%201%20-%20Danny's%20Diners/data)
- [SQLite Database](https://github.com/HanifaElahi/8-Weeks-SQL-Challenge-Solutions/blob/main/Case%20Study%201%20-%20Danny's%20Diners/data/DannysDiner.sqlite)
- [Danny's Diners Database Creation](https://github.com/HanifaElahi/8-Weeks-SQL-Challenge-Solutions/blob/main/Case%20Study%201%20-%20Danny's%20Diners/CASE%20STUDY%201%20-%20Danny's%20Diners%20Database%20Creation.ipynb)
- [Danny's Diner Solution](https://github.com/HanifaElahi/8-Weeks-SQL-Challenge-Solutions/blob/main/Case%20Study%201%20-%20Danny's%20Diners/CASE%20STUDY%201%20-%20Danny's%20Diner%20Solution.ipynb)
***

### 1. What is the total amount each customer spent at the restaurant?

````sql

SELECT s.customer_id AS Customer, SUM(m.price) AS "Total Amount Spent"
FROM sales s
INNER JOIN menu m
ON s.product_id = m.product_id
GROUP BY Customer;

````

#### Answer:

|   Customer  | Total Amount Spent |
| ----------- | ------------------ |
|      A      |        76          |
|      B      |        79          |
|      C      |        49          |

- Customer A spent $76.
- Customer B spent $79.
- Customer C spent $49.

***

### 2. How many days has each customer visited the restaurant?

````sql

SELECT customer_id AS Customer, COUNT(DISTINCT(order_date)) AS "Total Days"
FROM sales
GROUP BY Customer;

````

#### Answer:

| Customer | Total Days |
| -------- | ---------- |
|    A     |      6     |
|    B     |      6     |
|    C     |      4     |

- Customer A visited 6 times.
- Customer B visited 6 times.
- Customer C visited 4 times.

***

### 3. What was the first item from the menu purchased by each customer?

````sql

WITH first_items_purchased AS
(
    SELECT s.customer_id AS Customer, s.order_date AS Order_Date, m.product_name AS Product,
    DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
    FROM sales s
    INNER JOIN menu m
    ON s.product_id = m.product_id
    GROUP BY customer_id, order_date
) 

SELECT Customer, Product
FROM first_items_purchased
WHERE rank = 1;

````

#### Answer:

| Customer | Product | 
| -------- | ------- |
|     A    |  curry  | 
|     B    |  ramen  | 
|     C    |  curry  |

- Customer A and C's first order is curry.
- Customer B's first order is ramen.

***

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

````sql

SELECT m.product_name AS Item, COUNT(s.product_id) AS "Times Purchased"
FROM sales s
INNER JOIN menu m
ON s.product_id = m.product_id
GROUP BY s.product_id,Item
ORDER BY "Times Purchased" DESC
LIMIT 1;

````

#### Answer:

|  Item | Times Purchased | 
| ----- | --------------- |
| ramen |        7        |


- Most purchased item on the menu is ramen which is 7 times.

***

### 5. Which item was the most popular for each customer?

````sql

WITH most_popular_item AS 
(
    SELECT s.customer_id AS Customer, m.product_name AS Product, COUNT(m.product_id) AS Orders,
    DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY COUNT(s.customer_id) DESC) AS rank
    FROM menu m
    INNER JOIN sales s
    ON m.product_id = s.product_id
    GROUP BY Customer, Product
)
            
SELECT Customer, Product, Orders
FROM most_popular_item
WHERE rank = 1;

````

#### Answer:

| Customer | Product | Orders |
| -------- | ------- |------- |
|    A     |  ramen  |    3   |
|    B     |  curry  |    3   |
|    C     |  ramen  |    2   |

- Customer A and C's favourite item is ramen.
- Customer B's favourite item is curry.

***

### 6. Which item was purchased first by the customer after they became a member?

````sql

WITH first_puchase AS
(
    SELECT s.customer_id AS Customer, m.join_date AS Joining_Date, s.order_date AS Order_Date, s.product_id, me.product_name AS Product,
    DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
    FROM sales AS s
    INNER JOIN members AS m
    ON s.customer_id = m.customer_id
    INNER JOIN menu AS me
    ON s.product_id = me.product_id
    WHERE s.order_date >= m.join_date
)
            
SELECT Customer, Joining_Date, Order_Date, Product
FROM first_puchase
WHERE rank = 1;

````

#### Answer:

| Customer | Joining_Date | Order_Date | Product |
| -------- | ------------ | ---------- | ------- |
|    A     |  2020-01-06  | 2020-05-03 |  ramen  |
|    B     |  2020-01-07  | 2021-04-02 |  curry  |
|    C     |  2020-01-08  | 2020-02-02 |  ramen  |

- Customer A abd C's first order as member is ramen.
- Customer B's first order as member is curry.

***

### 7. Which item was purchased just before the customer became a member?

````sql

WITH puchase_cte AS
(
    SELECT s.customer_id AS Customer, m.join_date AS Joining_Date, s.order_date AS Order_Date, s.product_id, me.product_name AS Product,
    DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS rank
    FROM sales AS s
    INNER JOIN members AS m
    ON s.customer_id = m.customer_id
    INNER JOIN menu AS me
    ON s.product_id = me.product_id
    WHERE s.order_date < m.join_date
)
            
SELECT Customer, Order_Date, Product
FROM puchase_cte
WHERE rank = 1;

````

#### Answer:

| Customer | Order_Date  | Product |
| -------- | ----------- | ------- |
|     A    |  2019-01-02 |  curry  |
|     B    |  2020-01-03 |  curry  |
|     C    |  2019-03-07 |  curry  |

- All customers last order before becoming a member is curry.

***

### 8. What is the total items and amount spent for each member before they became a member?

````sql

SELECT s.customer_id AS Customer, COUNT(DISTINCT(s.product_id)) AS "Unique Menu Items", SUM(me.price) AS "Total Amount Spent ($)"
FROM sales s
INNER JOIN members m 
ON s.customer_id = m.customer_id
INNER JOIN menu me
ON s.product_id = me.product_id
WHERE s.order_date < m.join_date
GROUP BY Customer;

````

#### Answer:

| Customer | Unique Menu Items | Total Amount Spent ($) |
| -------- | ----------------- | ---------------------- |
|     A    |         1         |            15          |
|     B    |         2         |            27          |
|     C    |         1         |            15          |

Before becoming members,
- Customer A spent $15 on 1 item.
- Customer B spent $27 on 2 items.
- Customer C spent $15 on 1 item.

***

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier — how many points would each customer have?

````sql

WITH price_points AS
(
    SELECT *, 
        CASE
            WHEN m.product_id = 1 THEN m.price * 20
            ELSE m.price * 10
        END AS Points
    FROM menu m
    INNER JOIN sales AS s
    ON m.product_id = s.product_id
)
            
SELECT customer_id AS Customer, SUM(Points) AS "Total Points"
FROM price_points
GROUP BY Customer;

````

#### Answer:

| Customer | Total Points | 
| -------- | ------------ |
|     A    |      860     |
|     B    |      890     |
|     C    |      590     |

- Total points for Customer A is 860.
- Total points for Customer B is 890.
- Total points for Customer C is 590.

***

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?

````sql

WITH dates AS 
(
    SELECT *, date('2023-01-31') AS Last_Date
    FROM members 
),

    cte AS
(
    SELECT d.customer_id, s.order_date, d.join_date, sa.valid_date, d.last_date, m.product_name, m.price,
    SUM(CASE
          WHEN m.product_name = 'sushi' THEN 2 * 10 * m.price
          WHEN s.order_date BETWEEN d.join_date AND sa.valid_date THEN 2 * 10 * m.price
          ELSE 10 * m.price
          END) AS points

    FROM dates AS d
    INNER JOIN sales AS s
       ON d.customer_id = s.customer_id
    INNER JOIN sales_adjusted sa
        ON s.customer_id = sa.customer_id
    JOIN menu AS m
       ON s.product_id = m.product_id
    WHERE s.order_date < d.last_date
    GROUP BY d.customer_id, s.order_date, d.join_date, sa.valid_date, d.last_date, m.product_name, m.price
)

SELECT customer_id AS Customer, SUM(points) AS "Total Points"
FROM cte
WHERE Customer IN('A', 'B')
GROUP BY Customer;

````

**Assumptions Made:**
- On Day -X to Day 1 (customer becomes member on Day 1 ```join_date```) : Each customer's $1 spent is equal to 10 points while for sushi its 20 points.
- On Day 1 ```join_date``` to Day 7 ```valid_date```, each $1 spent for all items is 20 points.
- On Day 8 to ```last_day``` of Jan 2023, each $1 spent is 10 points and sushi is 20 points.

#### Answer:

| Customer | Total Points | 
| -------- | ------------ |
|     A    |     6660     |
|     B    |     6510     |

- Total points for Customer A is 6660.
- Total points for Customer B is 6510.

***

## BONUS QUESTIONS

### Join All The Things - Recreate the table with: customer_id, order_date, product_name, price, member (Y/N)

````sql

SELECT s.customer_id AS Customer, s.order_date "Order Date", m.product_name AS Product, m.price AS Price,
   CASE
      WHEN me.join_date > s.order_date THEN 'N'
      WHEN me.join_date <= s.order_date THEN 'Y'
      ELSE 'N'
      END AS Member
FROM sales AS s
LEFT JOIN menu AS m
   ON s.product_id = m.product_id
LEFT JOIN members AS me
   ON s.customer_id = me.customer_id;

 ````
 
#### Answer: 

| Customer | Order Date | Product | Price | Member |
| -------- | ---------- | --------| ----- | ------ |
|     A    | 2019-01-02 |  curry  |   15  |    N   |
|     A    | 2020-05-03 |  ramen  |   12  |    Y   |
|     A    | 2020-11-06 |  ramen  |   12  |    Y   |
|     A    | 2021-01-03 |  sushi  |   10  |    Y   |
|     A    | 2021-09-02 |  curry  |   15  |    Y   |
|     A    | 2022-05-03 |  ramen  |   12  |    Y   |
|     B    | 2019-05-02 |  ramen  |   12  |    N   |
|     B    | 2020-01-03 |  curry  |   15  |    N   |
|     B    | 2021-04-02 |  curry  |   15  |    Y   |
|     B    | 2022-01-03 |  curry  |   15  |    Y   |
|     B    | 2022-07-05 |  sushi  |   10  |    Y   |
|     B    | 2022-11-11 |  ramen  |   12  |    Y   |
|     C    | 2019-03-07 |  curry  |   15  |    N   |
|     C    | 2020-02-02 |  ramen  |   12  |    Y   |
|     C    | 2021-03-05 |  ramen  |   12  |    Y   |
|     C    | 2021-08-07 |  sushi  |   10  |    Y   |

***

### Rank All The Things - Danny also requires further information about the ```ranking``` of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ```ranking``` values for the records when customers are not yet part of the loyalty program.

````sql

WITH summary AS 
(
   SELECT s.customer_id AS Customer, s.order_date AS Order_Date, m.product_name AS Product, m.price AS Price,
      CASE
      WHEN me.join_date > s.order_date THEN 'N'
      WHEN me.join_date <= s.order_date THEN 'Y'
      ELSE 'N' END AS Member
   FROM sales AS s
   LEFT JOIN menu AS m
      ON s.product_id = m.product_id
   LEFT JOIN members AS me
      ON s.customer_id = me.customer_id
)

SELECT *, CASE
   WHEN member = 'N' then NULL
   ELSE
      RANK () OVER(PARTITION BY Customer, Member
      ORDER BY Order_Date) END AS Ranking
FROM summary;

````

#### Answer: 

| Customer | Order Date | Product | Price | Member | Ranking |
| -------- | ---------- | --------| ----- | ------ | ------- |
|     A    | 2019-01-02 |  curry  |   15  |    N   |   NULL  |
|     A    | 2020-05-03 |  ramen  |   12  |    Y   |    1    |
|     A    | 2020-11-06 |  ramen  |   12  |    Y   |    2    |
|     A    | 2021-01-03 |  sushi  |   10  |    Y   |    3    |
|     A    | 2021-09-02 |  curry  |   15  |    Y   |    4    |
|     A    | 2022-05-03 |  ramen  |   12  |    Y   |    5    |
|     B    | 2019-05-02 |  ramen  |   12  |    N   |   NULL  |
|     B    | 2020-01-03 |  curry  |   15  |    N   |   NULL  |
|     B    | 2021-04-02 |  curry  |   15  |    Y   |    1    |
|     B    | 2022-01-03 |  curry  |   15  |    Y   |    2    |
|     B    | 2022-07-05 |  sushi  |   10  |    Y   |    3    |
|     B    | 2022-11-11 |  ramen  |   12  |    Y   |    4    |
|     C    | 2019-03-07 |  curry  |   15  |    N   |   NULL  |
|     C    | 2020-02-02 |  ramen  |   12  |    Y   |    1    |
|     C    | 2021-03-05 |  ramen  |   12  |    Y   |    2    |
|     C    | 2021-08-07 |  sushi  |   10  |    Y   |    3    |

***
