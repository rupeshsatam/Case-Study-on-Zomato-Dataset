# SQL Practice Questions & Solutions

A comprehensive set of SQL queries based on a **food delivery platform** database (users, orders, restaurants, menu, food, delivery_partner, etc.).

## This case study covers essential SQL data cleaning techniques including:

- Creating backups

- Removing NULL values

- Data type conversions

- String manipulation with REPLACE and SUBSTRING_INDEX

- CASE statements for standardization

- Extracting numeric values from text

- Column management (ADD, MODIFY, DROP)

- Storage unit conversions (TB to GB)

## Questions and Answers


```sql
Q1
Question: How many total records are in the `order_details` table?
Answer:
SELECT COUNT(*) FROM order_details;



Q2.
Question:  How can we randomly sample 5 users from the users table?
Answer:
SELECT * FROM users 
ORDER BY rand() 
LIMIT 5;

Q3.
Question:  How to find NULL values in the restaurant_rating column of the orders table?
Answer:
SELECT * FROM orders 
WHERE restaurant_rating IS NULL;

Q4.
Question: How to replace NULL restaurant_rating values with 0?
Answer:
UPDATE orders 
SET restaurant_rating = 0 
WHERE restaurant_rating IS NULL;

Q5.
Question:  For each user, show the name and the total number of orders placed.
Answer:
SELECT t2.name, COUNT(*) AS '#orders'
FROM orders t1
JOIN users t2 ON t1.user_id = t2.user_id
GROUP BY t2.user_id;

Q6.
Question:  For each restaurant, show the name and the number of menu items offered.
Answer:
SELECT r_name, COUNT(*) AS 'menu_items'
FROM restaurants t1
JOIN menu t2 ON t1.r_id = t2.r_id
GROUP BY t2.r_id;

Q7.
Question: For each restaurant, show the name, number of votes (non-NULL ratings), and average rating (rounded to 2 decimals).
Answer:
SELECT r_name, 
       COUNT(*) AS 'num_votes', 
       ROUND(AVG(restaurant_rating), 2) AS 'rating'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
WHERE restaurant_rating IS NOT NULL
GROUP BY t1.r_id;

Q8.
Question: Which food item has been ordered the most number of times? Show the name and count.
Answer:
SELECT f_name, COUNT(*) AS order_count
FROM menu t1
JOIN food t2 ON t1.f_id = t2.f_id
GROUP BY t1.f_id
ORDER BY order_count DESC
LIMIT 1;

Q9. Which restaurant generated the highest revenue in the month of July?
Answer:
SELECT r_name, SUM(amount) AS 'revenue'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
WHERE MONTHNAME(DATE(date)) = 'July'
GROUP BY t1.r_id
ORDER BY revenue DESC
LIMIT 1;

Follow-up: Month-by-month revenue for restaurant box8
Answer:
SELECT MONTHNAME(DATE(date)) AS 'month', 
       SUM(amount) AS 'revenue'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
WHERE r_name = 'box8'
GROUP BY MONTHNAME(DATE(date))
ORDER BY MONTH(DATE(date));

Q10.
Question: Which restaurants have total revenue greater than 1500?
SELECT r_name, SUM(amount) AS 'revenue'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
GROUP BY t1.r_id
HAVING revenue > 1500;

Q11.
Question: Which users have never placed any order?
Answer:
SELECT user_id, name 
FROM users
EXCEPT
SELECT t1.user_id, name 
FROM orders t1
JOIN users ON t1.user_id = users.user_id;

Q12.
Question: Show order ID, food name, and date for user ID 5 who ordered between 2022-05-15 and 2022-07-15.
Answer:
SELECT t1.order_id, f_name, date
FROM orders t1
JOIN order_details t2 ON t1.order_id = t2.order_id
JOIN food t3 ON t2.f_id = t3.f_id
WHERE t1.user_id = 5 
  AND date BETWEEN '2022-05-15' AND '2022-07-15';

Q13.
Question: For each user and each food item, show how many times that user ordered that specific food.
Answer:
SELECT t1.user_id, t3.f_id, COUNT(*) AS order_count
FROM users t1
JOIN orders t2 ON t1.user_id = t2.user_id
JOIN order_details t3 ON t2.order_id = t3.order_id
GROUP BY t1.user_id, t3.f_id
ORDER BY order_count DESC;

Q14.
Question: Which restaurant has the lowest average menu item price?
Answer:
SELECT r_name, SUM(price)/COUNT(*) AS 'Avg_price'
FROM menu t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
GROUP BY t1.r_id
ORDER BY Avg_price ASC
LIMIT 1;

Q15.
Question: Calculate salary for each delivery partner: (number of orders × 100) + (average delivery rating × 1000). Show highest first.
vAnswer:
SELECT partner_name, 
       COUNT(*) * 100 + AVG(delivery_rating) * 1000 AS 'salary'
FROM orders t1
JOIN delivery_partner t2 ON t1.partner_id = t2.partner_id
GROUP BY t1.partner_id
ORDER BY salary DESC;

Q16.
Question: Find the correlation between delivery time and delivery rating.
SELECT CORR(delivery_time, delivery_rating) AS 'correlation'
FROM orders;

Q17.
Question: Which restaurants serve only vegetarian food?
Answer:
SELECT r_name
FROM menu t1
JOIN food t2 ON t1.f_id = t2.f_id
JOIN restaurants t3 ON t1.r_id = t3.r_id
GROUP BY t1.r_id
HAVING MIN(type) = 'Veg' AND MAX(type) = 'Veg';

Q18.
Question: For each user, show the name, minimum order amount, maximum order amount, and average order amount.
Answer:
SELECT name, 
       MIN(amount) AS min_amount,
       MAX(amount) AS max_amount,
       AVG(amount) AS avg_amount
FROM orders t1
JOIN users t2 ON t1.user_id = t2.user_id
GROUP BY t1.user_id;
