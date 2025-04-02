USE monday_coffee;

-- Q1: Total number of coffee consumers
SELECT SUM(total) AS total_coffee_consumer FROM 
(
    SELECT city_name, population, (population * 0.25) AS total FROM city
) AS c_c;

-- Q2: Number of coffee consumers per city (25% of the population)
SELECT city_name, (population * 0.25) AS coffee_consumer FROM city;

-- Q3: Total revenue from coffee sales in Q4 2023
SELECT SUM(total) AS total_revenue_Q4_2023 FROM 
(
    SELECT total, sale_date
    FROM sales
    WHERE sale_date BETWEEN '2023-09-01' AND '2023-12-31'
) AS Q4_2023;

-- Q4: Number of units of each coffee product sold
WITH cte AS (
    SELECT products.product_name, products.product_id, sales.total
    FROM products
    JOIN sales ON products.product_id = sales.product_id
)
SELECT product_name, COUNT(product_id) 
FROM cte
GROUP BY product_id;

-- Q5: Average sales amount per customer in each city
WITH cte AS (
    SELECT city.city_id, city.city_name, customers.customer_name, sales.customer_id, sales.total
    FROM city
    JOIN customers ON city.city_id = customers.city_id
    JOIN sales ON customers.customer_id = sales.customer_id
)
SELECT city_name, AVG(total) AS avg_sale_amt_per_city
FROM cte
GROUP BY city_name
ORDER BY avg_sale_amt_per_city DESC;

-- Q6: List of cities with populations and estimated coffee consumers
SELECT city_name, population, (population * 0.25) AS coffee_consumers
FROM city
ORDER BY city_name;

-- Q7: Top 3 selling products in each city based on sales volume
WITH cte AS (
    SELECT city_name, product_name, no_of_orders,
    RANK() OVER (PARTITION BY city_name ORDER BY no_of_orders DESC) AS product_rank 
    FROM (
        SELECT city_name, product_name, COUNT(total) AS no_of_orders 
        FROM (
            SELECT city.city_id, city.city_name, products.product_name, sales.total
            FROM city
            JOIN customers ON city.city_id = customers.city_id
            JOIN sales ON customers.customer_id = sales.customer_id
            JOIN products ON sales.product_id = products.product_id
        ) AS complete_data
        GROUP BY city_name, product_name
    ) AS xyz
)
SELECT * FROM cte WHERE product_rank IN (1, 2, 3);

-- Q8: Number of unique customers per city who purchased coffee products
WITH cte AS (
    SELECT city.city_name, customers.customer_name, products.product_name, products.product_id
    FROM city
    JOIN customers ON city.city_id = customers.city_id
    JOIN sales ON customers.customer_id = sales.customer_id
    JOIN products ON sales.product_id = products.product_id
)
SELECT city_name, COUNT(DISTINCT customer_name) AS unique_cx
FROM cte
WHERE product_id >= 14
GROUP BY city_name
ORDER BY unique_cx;

-- Q9: Find each city's average sale per customer and average rent per customer
WITH cte AS (
    SELECT city.city_name, SUM(sales.total) AS total_revenue,
    COUNT(DISTINCT sales.customer_id) AS total_CX,
    ROUND(SUM(sales.total) / COUNT(DISTINCT sales.customer_id), 2) AS avg_sale_per_cx
    FROM city
    JOIN customers ON city.city_id = customers.city_id
    JOIN sales ON customers.customer_id = sales.customer_id
    GROUP BY city.city_name
),
city_rent AS (
    SELECT city_name, estimated_rent FROM city
)
SELECT city_rent.city_name, city_rent.estimated_rent, cte.total_CX, 
cte.avg_sale_per_cx, (city_rent.estimated_rent / cte.total_CX) AS avg_rent_per_cx
FROM city_rent
JOIN cte ON city_rent.city_name = cte.city_name;

-- Q10: Monthly sales growth percentage
WITH cte AS (
    SELECT city.city_name, EXTRACT(MONTH FROM sale_date) AS month, 
    EXTRACT(YEAR FROM sale_date) AS year, SUM(sales.total) AS total_sale
    FROM city
    JOIN customers ON city.city_id = customers.city_id
    JOIN sales ON customers.customer_id = sales.customer_id
    GROUP BY city_name, month, year
    ORDER BY city_name, year, month
),
growth_ratio AS (
    SELECT city_name, month, year, total_sale,
    LAG(total_sale) OVER (PARTITION BY city_name ORDER BY year, month) AS last_month_sale
    FROM cte
)
SELECT city_name, month, year, total_sale, last_month_sale, 
ROUND(((total_sale - last_month_sale) / last_month_sale) * 100, 2) AS growth_ratio
FROM growth_ratio
WHERE last_month_sale IS NOT NULL;
