# SQL Practice Questions & Solutions

A comprehensive set of SQL queries based on a **food delivery platform** database (users, orders, restaurants, menu, food, delivery_partner, etc.).

## Questions and Answers


```sql
Q1 How many total records are in the `order_details` table?
SELECT COUNT(*) FROM order_details;

How many total records are in the `order_details` table?
SELECT COUNT(*) FROM order_details;

Q2. How can we randomly sample 5 users from the users table?
SELECT * FROM users 
ORDER BY rand() 
LIMIT 5;

Q3. How to find NULL values in the restaurant_rating column of the orders table?
SELECT * FROM orders 
WHERE restaurant_rating IS NULL;

Q4. How to replace NULL restaurant_rating values with 0?
UPDATE orders 
SET restaurant_rating = 0 
WHERE restaurant_rating IS NULL;

Q5. For each user, show the name and the total number of orders placed.
SELECT t2.name, COUNT(*) AS '#orders'
FROM orders t1
JOIN users t2 ON t1.user_id = t2.user_id
GROUP BY t2.user_id;

Q6. For each restaurant, show the name and the number of menu items offered.
SELECT r_name, COUNT(*) AS 'menu_items'
FROM restaurants t1
JOIN menu t2 ON t1.r_id = t2.r_id
GROUP BY t2.r_id;

Q7. For each restaurant, show the name, number of votes (non-NULL ratings), and average rating (rounded to 2 decimals).
SELECT r_name, 
       COUNT(*) AS 'num_votes', 
       ROUND(AVG(restaurant_rating), 2) AS 'rating'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
WHERE restaurant_rating IS NOT NULL
GROUP BY t1.r_id;

Q8. Which food item has been ordered the most number of times? Show the name and count.
SELECT f_name, COUNT(*) AS order_count
FROM menu t1
JOIN food t2 ON t1.f_id = t2.f_id
GROUP BY t1.f_id
ORDER BY order_count DESC
LIMIT 1;

Q9. Which restaurant generated the highest revenue in the month of July?
SELECT r_name, SUM(amount) AS 'revenue'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
WHERE MONTHNAME(DATE(date)) = 'July'
GROUP BY t1.r_id
ORDER BY revenue DESC
LIMIT 1;

Follow-up: Month-by-month revenue for restaurant box8
SELECT MONTHNAME(DATE(date)) AS 'month', 
       SUM(amount) AS 'revenue'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
WHERE r_name = 'box8'
GROUP BY MONTHNAME(DATE(date))
ORDER BY MONTH(DATE(date));

Q10. Which restaurants have total revenue greater than 1500?
SELECT r_name, SUM(amount) AS 'revenue'
FROM orders t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
GROUP BY t1.r_id
HAVING revenue > 1500;

Q11. Which users have never placed any order?
SELECT user_id, name 
FROM users
EXCEPT
SELECT t1.user_id, name 
FROM orders t1
JOIN users ON t1.user_id = users.user_id;

Q12. Show order ID, food name, and date for user ID 5 who ordered between 2022-05-15 and 2022-07-15.
SELECT t1.order_id, f_name, date
FROM orders t1
JOIN order_details t2 ON t1.order_id = t2.order_id
JOIN food t3 ON t2.f_id = t3.f_id
WHERE t1.user_id = 5 
  AND date BETWEEN '2022-05-15' AND '2022-07-15';

Q13. For each user and each food item, show how many times that user ordered that specific food.
SELECT t1.user_id, t3.f_id, COUNT(*) AS order_count
FROM users t1
JOIN orders t2 ON t1.user_id = t2.user_id
JOIN order_details t3 ON t2.order_id = t3.order_id
GROUP BY t1.user_id, t3.f_id
ORDER BY order_count DESC;

Q14. Which restaurant has the lowest average menu item price?
SELECT r_name, SUM(price)/COUNT(*) AS 'Avg_price'
FROM menu t1
JOIN restaurants t2 ON t1.r_id = t2.r_id
GROUP BY t1.r_id
ORDER BY Avg_price ASC
LIMIT 1;

Q15. Calculate salary for each delivery partner: (number of orders × 100) + (average delivery rating × 1000). Show highest first.
SELECT partner_name, 
       COUNT(*) * 100 + AVG(delivery_rating) * 1000 AS 'salary'
FROM orders t1
JOIN delivery_partner t2 ON t1.partner_id = t2.partner_id
GROUP BY t1.partner_id
ORDER BY salary DESC;

Q16. Find the correlation between delivery time and delivery rating.
SELECT CORR(delivery_time, delivery_rating) AS 'correlation'
FROM orders;

Q17. Which restaurants serve only vegetarian food?
SELECT r_name
FROM menu t1
JOIN food t2 ON t1.f_id = t2.f_id
JOIN restaurants t3 ON t1.r_id = t3.r_id
GROUP BY t1.r_id
HAVING MIN(type) = 'Veg' AND MAX(type) = 'Veg';

Q18. For each user, show the name, minimum order amount, maximum order amount, and average order amount.
SELECT name, 
       MIN(amount) AS min_amount,
       MAX(amount) AS max_amount,
       AVG(amount) AS avg_amount
FROM orders t1
JOIN users t2 ON t1.user_id = t2.user_id
GROUP BY t1.user_id;
