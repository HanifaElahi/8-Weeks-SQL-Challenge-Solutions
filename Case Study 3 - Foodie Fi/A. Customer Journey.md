# 🥑 Case Study 3 - Foodie Fi

# 🎞 A. Customer Journey

<p>
    <a href="#"><img alt="SQL" src="https://img.shields.io/badge/SQL-018bff.svg?&style=for-the-badge&logo=sql&logoColor=white"></a>
    <a href="#"><img alt="Python" src="https://img.shields.io/badge/Python-F02E65?style=for-the-badge&logo=Python&logoColor=white"></a>
    <a href="#"><img alt="SQLite" src="https://img.shields.io/badge/SQLite-311C87?style=for-the-badge&logo=sqlite&logoColor=white"></a>
    <a href="#"><img alt="Jupyter" src="https://img.shields.io/badge/Jupyter-FF6600.svg?&style=for-the-badge&logo=Jupyter&logoColor=white"></a>
</p>

## Solution

***

### Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

<img width="261" src="https://user-images.githubusercontent.com/81607668/129756709-75919d79-e1cd-4187-a129-bdf90a65e196.png">

````sql

SELECT
  s.customer_id,p.plan_id, p.plan_name,  s.start_date
FROM plans p
JOIN subscriptions s
  ON p.plan_id = s.plan_id
WHERE s.customer_id IN (1,2,11,13,15,16,18,19)

````

#### Answer:

| customer_id | plan_id |   plan_name   | start_date |
| ----------- | ------- | ------------- | ---------- |
|      1      |    0    |     trial     | 2020-08-01 |
|      1      |    1    | basic monthly | 2020-08-08 |
|      2      |    0    |     trial     | 2020-09-20 |
|      2      |    3    |   pro annual  | 2020-09-27 |
|     11      |    0    |     trial     | 2020-11-19 |
|     11      |    4    |     churn     | 2020-11-26 |
|     13      |    0    |     trial     | 2020-12-15 |
|     13      |    1    | basic monthly | 2020-12-22 |
|     13      |    2    |  pro monthly  | 2021-03-29 |
|     15      |    0    |     trial     | 2020-03-17 |
|     15      |    2    |  pro monthly  | 2020-03-24 |
|     15      |    4    |     chrun     | 2020-04-29 |
|     16      |    0    |     trial     | 2020-05-31 |
|     16      |    1    | basic monthly | 2020-06-07 |
|     16      |    3    |   pro annual  | 2020-10-21 |
|     18      |    0    |     trial     | 2020-07-06 |
|     18      |    2    |  pro monthly  | 2020-07-13 |
|     19      |    0    |     trial     | 2020-06-22 |
|     19      |    2    |  pro monthly  | 2020-06-29 |
|     19      |    3    |   pro annual  | 2020-08-29 |

- Customer 1 started the free trial on 1 Aug 2020 and subsequently subscribed to the basic monthly plan on 8 Aug 2020 after the 7 - days trial has ended.
- Customer 2 started the free trial on 20 Sep 2020 and subsequently subscribed to the pro annual plan on 27 Sep 2020 after the 7 - days trial has ended.
- Customer 11 started the free trial on 19 Nov 2020 and subsequently churned on 26 Nov 2020 after the 7 - days trial.
- Customer 13 started the free trial on 15 Dec 2020 and then subscribed to the basic monthly plan on 22 Dec 2020. 3 months later, on 29 Mar 2021, customer upgraded to the pro monthly plan.
- Customer 15 commenced free trial on 17 Mar 2020, then upgraded to pro monthly plan on 24 Mar 2020 after the trial ended. In the following month on 29 Apr 2020, the customer terminated subscription and churned until the paid subscription ended on 24/25 May 2020.
- Customer 16 started the free trial on 31 May 2020 and then subscribed to the basic monthly plan on 7 June 2020. 4 months later, on 21 Oct 2020, customer upgraded to the pro annual plan.
- Customer 18 started the free trial on 6 July 2020 and subsequently subscribed to the pro monthly plan on 13 July 2020 after the 7 - days trial has ended.
- Customer 19 started the free trial on 22 June 2020 and then subscribed to the pro monthly plan on 29 June 2020. 2 months later, on 29 Oct 2020, customer upgraded to the pro annual plan.

***

