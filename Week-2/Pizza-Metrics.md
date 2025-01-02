# 8-Weeks-SQL-Challenge
Completing the 8 Weeks SQL Challenge

Question 1: How many pizzas were ordered?
```SQL
SELECT COUNT(pizza_id) AS num_of_pizzas
FROM customer_orders
```

Results:
| num_of_pizzas |
| ------------- |
| 14            |

Question 2: How many unique customer orders were made?
```SQL
SELECT COUNT(DISTINCT order_id) AS num_of_orders
FROM customer_orders
```

Results:
| num_of_orders |
| ------------- |
| 10            |

Question 3: How many successful orders were delivered by each runner?
```SQL
SELECT runner_id, COUNT(order_id)
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id
ORDER BY runner_id
```

Results:
| runner_id | count |
| --------- | ----- |
| 1         | 4     |
| 2         | 3     |
| 3         | 1     |

Question 4: How many of each type of pizza was delivered?
```SQL
SELECT c.pizza_id, pizza_name, COUNT(*)
FROM runner_orders r JOIN customer_orders c
ON r.order_id = c.order_id
JOIN pizza_names p
ON c.pizza_id = p.pizza_id
WHERE cancellation IS NULL
GROUP BY c.pizza_id, pizza_name
ORDER BY c.pizza_id
```

Results:
| pizza_id | pizza_name | count |
| -------- | ---------- | ----- |
| 1        | Meatlovers | 9     |
| 2        | Vegetarian | 3     |

Question 5: How many Vegetarian and Meatlovers were ordered by each customer?
```SQL
SELECT customer_id, c.pizza_id, pizza_name, COUNT(*)
FROM runner_orders r JOIN customer_orders c
ON r.order_id = c.order_id
JOIN pizza_names p
ON c.pizza_id = p.pizza_id
WHERE cancellation IS NULL
GROUP BY customer_id, c.pizza_id, pizza_name
ORDER BY customer_id, c.pizza_id
```

Results:
| customer_id | pizza_id | pizza_name | count |
| ----------- | -------- | ---------- | ----- |
| 101         | 1        | Meatlovers | 2     |
| 102         | 1        | Meatlovers | 2     |
| 102         | 2        | Vegetarian | 1     |
| 103         | 1        | Meatlovers | 2     |
| 103         | 2        | Vegetarian | 1     |
| 104         | 1        | Meatlovers | 3     |
| 105         | 2        | Vegetarian | 1     |

Question 6: What was the maximum number of pizzas delivered in a single order?
```SQL
SELECT num_of_pizzas AS max_num_pizzas
FROM (
  SELECT c.order_id, COUNT(pizza_id) AS num_of_pizzas, DENSE_RANK() OVER(ORDER BY COUNT(pizza_id) DESC) as rank_order
	FROM customer_orders c JOIN runner_orders r
  	ON c.order_id = r.order_id
    	WHERE cancellation IS NULL
	GROUP BY c.order_id
) rk
WHERE rank_order = 1
```

Results:
| max_num_pizzas |
| -------------- |
| 3              |

Question 7: For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```SQL
SELECT customer_id, 
    SUM(CASE WHEN (exclusions IS NULL) AND (extras IS NULL) THEN 1 ELSE 0 END) AS no_change, 
    SUM(CASE WHEN (exclusions IS NOT NULL) OR (extras IS NOT NULL) THEN 1 ELSE 0 END) AS changed
FROM customer_orders c JOIN runner_orders r
ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY customer_id
ORDER BY customer_id
```

Results:
| customer_id | no_change | changed |
| ----------- | --------- | ------- |
| 101         | 2         | 0       |
| 102         | 3         | 0       |
| 103         | 0         | 3       |
| 104         | 1         | 2       |
| 105         | 0         | 1       |

Question 8: How many pizzas were delivered that had both exclusions and extras?
```SQL
SELECT COUNT(pizza_id) AS num_of_pizzas
FROM customer_orders c JOIN runner_orders r
ON c.order_id = r.order_id
WHERE cancellation IS NULL AND exclusions IS NOT NULL AND extras IS NOT NULL
```

Results:
| num_of_pizzas |
| ------------- |
| 1             |

Question 9: What was the total volume of pizzas ordered for each hour of the day?
```SQL
SELECT EXTRACT(HOUR FROM order_time) AS hour_of_day, COUNT(pizza_id) AS num_of_pizzas
FROM customer_orders
GROUP BY EXTRACT(HOUR FROM order_time)
ORDER BY EXTRACT(HOUR FROM order_time)
```

Results:
| hour_of_day | num_of_pizzas |
| ----------- | ------------- |
| 11          | 1             |
| 13          | 3             |
| 18          | 3             |
| 19          | 1             |
| 21          | 3             |
| 23          | 3             |

Question 10: What was the volume of orders for each day of the week?
```SQL
SELECT TO_CHAR(order_time, 'Day') AS day_of_week, 
COUNT(pizza_id) AS num_of_pizzas
FROM customer_orders
GROUP BY TO_CHAR(order_time, 'Day'), EXTRACT(DOW FROM order_time)
ORDER BY EXTRACT(DOW FROM order_time)
```

Results:
| day_of_week | num_of_pizzas |
| ----------- | ------------- |
| Wednesday   | 5             |
| Thursday    | 3             |
| Friday      | 1             |
| Saturday    | 5             |
