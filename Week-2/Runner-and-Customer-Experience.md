Question 1: How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```SQL
SELECT week_start, COUNT(runner_id) AS num_of_runners
FROM(
	SELECT *, '2021-01-01'::date + (FLOOR(EXTRACT(DAY FROM registration_date::timestamp - '2021-01-01'::timestamp)/7)*7)::INT AS week_start
	FROM runners
) w
GROUP BY week_start
ORDER BY week_start
```

Results:
| week_start | num_of_runners |
| ---------- | -------------- |
| 2021-01-01 | 2              |
| 2021-01-08 | 1              |
| 2021-01-15 | 1              |

Question 2: What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```SQL
SELECT runner_id, ROUND(AVG(duration)::numeric,2) AS avg_duration
FROM(
  SELECT runner_id, c.order_id, MAX(EXTRACT(MINUTE FROM pickup_time::timestamp - order_time::timestamp)) AS duration
  FROM customer_orders c JOIN runner_orders r
  ON c.order_id = r.order_id
  WHERE pickup_time IS NOT NULL
  GROUP BY runner_id, c.order_id
) avg_d
GROUP BY runner_id
ORDER BY runner_id
```

Results:
| runner_id | avg_duration |
| --------- | ------------ |
| 1         | 14.00        |
| 2         | 19.67        |
| 3         | 10.00        |

Question 3: Is there any relationship between the number of pizzas and how long the order takes to prepare?
```SQL
SELECT num_of_pizzas, AVG(duration) AS avg_duration_order
FROM (
  SELECT c.order_id, COUNT(pizza_id) AS num_of_pizzas, MAX(EXTRACT(MINUTE FROM pickup_time::timestamp - order_time::timestamp)) AS duration
  FROM customer_orders c JOIN runner_orders r
  ON c.order_id = r.order_id
  WHERE cancellation IS NULL
  GROUP BY c.order_id
) duration
GROUP BY num_of_pizzas
ORDER BY num_of_pizzas;
```

Results: 
| num_of_pizzas | avg_duration_order |
| ------------- | ------------------ |
| 1             | 12                 |
| 2             | 18                 |
| 3             | 29                 |

Question 4: What was the average distance travelled for each customer?
```SQL
SELECT customer_id, AVG(distance) as avg_distance
FROM (
  SELECT customer_id, order_id
  FROM customer_orders c
  GROUP BY customer_id, order_id
) gb JOIN runner_orders r
ON gb.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY customer_id
ORDER BY customer_id
```

Results:
| customer_id | avg_distance |
| ----------- | ------------ |
| 101         | 20           |
| 102         | 18.4         |
| 103         | 23.4         |
| 104         | 10           |
| 105         | 25           |

Question 5: What was the difference between the longest and shortest delivery times for all orders?
```SQL
SELECT MAX(duration) AS max_duration, MIN(duration) AS min_duration, MAX(duration) - MIN(duration) AS min_max_diff
FROM runner_orders
```

Results: 
| max_duration | min_duration | min_max_diff |
| ------------ | ------------ | ------------ |
| 40           | 10           | 30           |

Question 6: What was the average speed for each runner for each delivery and do you notice any trend for these values?
```SQL
SELECT runner_id, c.order_id,  ROUND((distance/(duration/60))::numeric, 2) AS "velocity (km/h)"
FROM customer_orders c JOIN runner_orders r
ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY c.order_id, runner_id, distance, duration
ORDER BY runner_id, "velocity (km/h)"
```

Results: 
| runner_id | order_id | velocity (km/h) |
| --------- | -------- | --------------- |
| 1         | 1        | 37.50           |
| 1         | 3        | 40.20           |
| 1         | 2        | 44.44           |
| 1         | 10       | 60.00           |
| 2         | 4        | 35.10           |
| 2         | 7        | 60.00           |
| 2         | 8        | 93.60           |
| 3         | 5        | 40.00           |

Question 7: What is the successful delivery percentage for each runner?
```SQL
SELECT runner_id, SUM(CASE WHEN cancellation IS NULL THEN 1 ELSE 0 END)::float/COUNT(order_id)::float AS success_rate
FROM runner_orders
GROUP BY runner_id
ORDER BY runner_id
```

Results: 
| runner_id | success_rate |
| --------- | ------------ |
| 1         | 1            |
| 2         | 0.75         |
| 3         | 0.5          |
