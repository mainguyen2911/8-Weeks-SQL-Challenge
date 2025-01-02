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
