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
Question 4: What was the average distance travelled for each customer?
Question 5: What was the difference between the longest and shortest delivery times for all orders?
Question 6: What was the average speed for each runner for each delivery and do you notice any trend for these values?
Question 7: What is the successful delivery percentage for each runner?
