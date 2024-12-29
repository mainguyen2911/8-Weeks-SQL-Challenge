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
Question 4: How many of each type of pizza was delivered?
Question 5: How many Vegetarian and Meatlovers were ordered by each customer?
Question 6: What was the maximum number of pizzas delivered in a single order?
Question 7: For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
Question 8: How many pizzas were delivered that had both exclusions and extras?
Question 9: What was the total volume of pizzas ordered for each hour of the day?
Question 10: What was the volume of orders for each day of the week?
