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
SELECT rp.product_id, product_name, num_orders
FROM (
    SELECT product_id, DENSE_RANK() OVER(ORDER BY COUNT(*) DESC) AS product_rank, COUNT(*) AS num_orders
    FROM dannys_diner.sales
    GROUP BY product_id
) rp JOIN dannys_diner.menu m
ON rp.product_id = m.product_id
WHERE product_rank = 1
```
Results:
| product_id | product_name | num_orders |
| ---------- | ------------ | ---------- |
| 3          | ramen        | 8          |

Question 5: Which item was the most popular for each customer?
``` SQL
SELECT customer_id, rp.product_id, product_name, num_orders
FROM (
  SELECT customer_id, product_id, DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(*) DESC) AS product_rank, COUNT(*) AS num_orders
  FROM dannys_diner.sales
  GROUP BY customer_id, product_id
) rp JOIN dannys_diner.menu m
ON rp.product_id = m.product_id
WHERE product_rank = 1
ORDER BY customer_id
```

Results:
| customer_id | product_id | product_name | num_orders |
| ----------- | ---------- | ------------ | ---------- |
| A           | 3          | ramen        | 3          |
| B           | 1          | sushi        | 2          |
| B           | 2          | curry        | 2          |
| B           | 3          | ramen        | 2          |
| C           | 3          | ramen        | 3          |

Question 6: Which item was purchased first by the customer after they became a member?
```SQL
WITH CTE AS(
  SELECT s.customer_id, product_id, DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY order_date) AS date_rank
  FROM dannys_diner.sales s LEFT JOIN dannys_diner.members mb
  ON s.customer_id = mb.customer_id
  WHERE order_date >= join_date
)
SELECT customer_id, c.product_id, product_name
FROM CTE c JOIN dannys_diner.menu m
ON c.product_id = m.product_id
WHERE date_rank = 1
ORDER BY customer_id
```

Results:
| customer_id | product_id | product_name |
| ----------- | ---------- | ------------ |
| A           | 2          | curry        |
| B           | 1          | sushi        |

Question 7: Which item was purchased just before the customer became a member?
``` SQL
WITH CTE AS(
  SELECT s.customer_id, product_id, DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY order_date DESC) AS date_rank
  FROM dannys_diner.sales s LEFT JOIN dannys_diner.members mb
  ON s.customer_id = mb.customer_id
  WHERE order_date < join_date
)
SELECT customer_id, c.product_id, product_name
FROM CTE c JOIN dannys_diner.menu m
ON c.product_id = m.product_id
WHERE date_rank = 1
ORDER BY customer_id
```

Results:
| customer_id | product_id | product_name |
| ----------- | ---------- | ------------ |
| A           | 1          | sushi        |
| A           | 2          | curry        |
| B           | 1          | sushi        |

Question 8: What is the total items and amount spent for each member before they became a member?
```SQL
SELECT s.customer_id, COUNT(*) AS total_orders, SUM(price) AS total_value
FROM dannys_diner.sales s LEFT JOIN dannys_diner.members mb
ON s.customer_id = mb.customer_id
JOIN dannys_diner.menu m
ON s.product_id = m.product_id
WHERE order_date < join_date
GROUP BY s.customer_id
ORDER BY s.customer_id
```

Results:
| customer_id | total_orders | total_value |
| ----------- | ------------ | ----------- |
| A           | 2            | 25          |
| B           | 3            | 40          |

Question 9:  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```SQL
SELECT customer_id, SUM(CASE WHEN product_name='sushi' THEN 10*2*price ELSE 10*price END) AS points
FROM dannys_diner.sales s JOIN dannys_diner.menu m
ON s.product_id = m.product_id
GROUP BY customer_id
ORDER BY customer_id
```

Results:
| customer_id | points |
| ----------- | ------ |
| A           | 860    |
| B           | 940    |
| C           | 360    |

Question 10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
```SQL
SELECT s.customer_id, SUM(
  CASE WHEN (product_name='sushi') OR (order_date BETWEEN join_date AND join_date + 6) THEN 10*2*price ELSE 10*price END) AS points
FROM dannys_diner.sales s JOIN dannys_diner.menu m
ON s.product_id = m.product_id
JOIN dannys_diner.members mb
ON s.customer_id = mb.customer_id
WHERE order_date <= '2021-01-31'
GROUP BY s.customer_id
ORDER BY s.customer_id
```

Results:
| customer_id | points |
| ----------- | ------ |
| A           | 1370   |
| B           | 820    |
