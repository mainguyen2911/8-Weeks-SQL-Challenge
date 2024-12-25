# 8-Weeks-SQL-Challenge
Completing the 8 Weeks SQL Challenge

Question 1: What is the total amount each customer spent at the restaurant?
``` SQL
SELECT
  	customer_id, SUM(price) AS total_sales
FROM dannys_diner.sales s LEFT JOIN dannys_diner.menu m
ON s.product_id = m.product_id
GROUP BY customer_id
ORDER BY customer_id;
```
Results:
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

Question 2: How many days has each customer visited the restaurant?
``` SQL
SELECT
  	customer_id, COUNT(DISTINCT order_date) AS num_of_visited_days
FROM dannys_diner.sales
GROUP BY customer_id
ORDER BY customer_id;
```
Results:
| customer_id | num_of_visited_days |
| ----------- | ------------------- |
| A           | 4                   |
| B           | 6                   |
| C           | 2                   |

Question 3: What was the first item from the menu purchased by each customer?
``` SQL
SELECT
    s.customer_id, s.product_id, m.product_name
FROM dannys_diner.sales s JOIN (
    SELECT customer_id, MIN(order_date) AS first_date
    FROM dannys_diner.sales
    GROUP BY customer_id
) fd
ON s.customer_id = fd.customer_id AND s.order_date = fd.first_date
LEFT JOIN dannys_diner.menu m
ON s.product_id = m.product_id
ORDER BY s.customer_id, s.product_id
```
Results:
| customer_id | product_id | product_name |
| ----------- | ---------- | ------------ |
| A           | 1          | sushi        |
| A           | 2          | curry        |
| B           | 2          | curry        |
| C           | 3          | ramen        |
| C           | 3          | ramen        |

Question 4: What is the most purchased item on the menu and how many times was it purchased by all customers?
``` SQL
SELECT m.product_id, m.product_name, COUNT(customer_id) AS total_purchases
FROM dannys_diner.sales s RIGHT JOIN dannys_diner.menu m
ON s.product_id = m.product_id
GROUP BY m.product_id, m.product_name
HAVING COUNT(customer_id) = (
  SELECT MAX(num_purchases) AS max_purchases
  FROM (
    SELECT COUNT(customer_id) AS num_purchases
    FROM dannys_diner.sales
  	GROUP BY product_id
  )mx
)
```
Results:
| product_id | product_name | total_purchases |
| ---------- | ------------ | --------------- |
| 3          | ramen        | 8               |

Question 5: Which item was the most popular for each customer?
