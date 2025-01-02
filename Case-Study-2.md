# 8-Weeks-SQL-Challenge
Completing the 8 Weeks SQL Challenge
Data Cleaning:
```SQL
-- Clean customer_orders table  

UPDATE customer_orders
SET exclusions = NULL
WHERE exclusions = '' OR exclusions = 'null';

UPDATE customer_orders
SET extras = NULL
WHERE extras = '' OR extras = 'null';

-- Clean runner_orders table

UPDATE runner_orders
SET pickup_time = NULL
WHERE pickup_time = '' OR pickup_time = 'null';

UPDATE runner_orders
SET distance = CASE 
	WHEN distance = '' OR distance = 'null' THEN NULL
    WHEN TRIM(distance) LIKE '%km' THEN REPLACE(TRIM(distance), 'km', '')
    ELSE distance
    END;

UPDATE runner_orders
SET duration = CASE
	WHEN duration = '' OR duration = 'null' THEN NULL
    WHEN TRIM(duration) LIKE '%mins' THEN REPLACE(TRIM(duration), 'mins', '')
    WHEN TRIM(duration) LIKE '%minute' THEN REPLACE(TRIM(duration), 'minute', '')
    WHEN TRIM(duration) LIKE '%minutes' THEN REPLACE(TRIM(duration), 'minutes', '')
    ELSE duration
    END;

UPDATE runner_orders
SET cancellation = NULL
WHERE cancellation = '' OR cancellation = 'null';

ALTER TABLE runner_orders
ALTER COLUMN distance TYPE FLOAT USING (distance::float);

ALTER TABLE runner_orders
ALTER COLUMN duration TYPE FLOAT USING (duration::float);
```
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
SELECT pizza_id, COUNT(*)
FROM runner_orders r JOIN customer_orders c
ON r.order_id = c.order_id
WHERE cancellation IS NULL
GROUP BY pizza_id
ORDER BY pizza_id
```

Results:
| pizza_id | count |
| -------- | ----- |
| 1        | 9     |
| 2        | 3     |

Question 5: How many Vegetarian and Meatlovers were ordered by each customer?
Question 6: What was the maximum number of pizzas delivered in a single order?
Question 7: For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
Question 8: How many pizzas were delivered that had both exclusions and extras?
Question 9: What was the total volume of pizzas ordered for each hour of the day?
Question 10: What was the volume of orders for each day of the week?
