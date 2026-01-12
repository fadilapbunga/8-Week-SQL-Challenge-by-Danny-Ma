# 1️⃣ Case Study #1 - Danny's Diner
- Dataset: Case Study #1 - Danny's Diner  
- Source: DB Fiddle https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138  
- Queried using: Microsoft SQL Server Management Studio 

***

## Database Schema
This case study uses three tables:
- `sales` : The sales table captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.
- `menu` : The menu table maps the product_id to the actual product_name and price of each menu item.
- `members` : The final members table captures the join_date when a customer_id joined the beta version of the Danny’s Diner loyalty program.  
The database schema for this case study can be found here:
👉 [schema.sql](schema.sql)

## The Situation
- The situation is that we have three tables in a database: sales, menu, and members. 
- The sales table contains information about customer, orders, including the customer ID, order date, and product ID. 
- The menu table contains, information about the products available for purchase, including the product ID,  product name, and price. 
- The members table contains information about customers who are members,  including the customer ID and join date.

📎 __Please refer to the following sources for more detailed information: [8 Week SQL Challenge](https://8weeksqlchallenge.com/)__

***

## Entity Relationship Diagram
<div align="center"><img width="763" height="375" alt="image" src="https://github.com/user-attachments/assets/a056ac13-c539-4c31-8ad0-1154182eca36" /></ins></div>

***

## Case Study Question and Answer

### 1. What is the total amount each customer spent at the restaurant?

````sql
SELECT
	sales.customer_id,
	SUM(menu.price) AS total_price
FROM dannys_dinner_db.dbo.sales
INNER JOIN dannys_dinner_db.dbo.menu
	ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id ASC;
````
#### Steps
- Since we want to calculate the amount each customer spent, we need data from two tables: __‘sales’__ because it contains a summary of each sale, and __‘menu’__ because the price of each product is listed in that table.
- Therefore, the __SELECT__ statement chosen is __sales.customer_id__ (referring to each customer) and __SUM(menu.price) AS total_price__ because we want to add up the prices for each customer.
- Use __INNER JOIN__ to combine the two data sets from the ‘sales’ and ‘menu’ tables.
- Group the aggregated results by __sales.customer_id__ using __GROUP BY__.
- __Sort the data__ based on the largest to smallest sales result using __ORDER BY__.

#### Answer
- Customer A spent $76
- Customer B spent $74
- Customer C spent $36

---
### 2. How many days has each customer visited the restaurant?

````sql
SELECT
	customer_id,
	COUNT(DISTINCT order_date) AS days_visit
FROM dannys_dinner_db.dbo.sales
GROUP BY customer_id;
````
#### Steps
- To calculate how many days a customer has visited the restaurant, we need data from the __‘sales’__ table, specifically the __customer_id and order_date columns__ (to know when the customer placed their order).
- Since we are only counting the number of days, not the number of orders, use __DISTINCT__.
- DISTINCT is necessary because if a customer places more than one order in a single day, DISTINCT is needed to retrieve data for each unique order date.
- Group the aggregated results by sales.customer_id using GROUP BY.

#### Answer
- Customer A has visited for 4 days.
- Customer B has visited for 6 days.
- Customer A has visited for 2 days.

---
### 3. What was the first item from the menu purchased by each customer?

````sql
WITH first_order AS (
SELECT
	sales.customer_id,
	sales.order_date,
	menu.product_name,
	DENSE_RANK() OVER (
		PARTITION BY sales.customer_id
		ORDER BY sales.order_date) AS ranking
FROM dannys_dinner_db.dbo.sales
INNER JOIN dannys_dinner_db.dbo.menu
	ON sales.product_id = menu.product_id
)

SELECT
	customer_id,
	product_name
FROM first_order
WHERE ranking = 1;
````
### Steps
- This SQL Query uses __a common table expression (CTE)__ which creates a temporary query result named __first_order__.
- This temporary query named first_order __combines__ the __sales__ and __menu tables__ and __sorts customer purchases__ by adding a new column called __‘ranking’__ using __DENSE RANK()__.
- Since it will calculate the ranking per customer, use __PARTITION by sales.customer_id__.
- Then __sort__ this CTE with __ORDER by sales.order_date__ or according to the order date from oldest to newest.
- The final query then __filters__ the CTE result to retrieve __only the first purchased item per customer__ with the condition __WHERE ranking = 1__.

‼️ __The use of DENSE RANK () is more recommended because if a customer buys more than one item on the first day, DENSE RANK () does not “delete” the menu items purchased at the same time.__

### Answer
- First item from the menu purchased by customer A are sushi and curry
- First item from the menu purchased by customer B is curry
- First item from the menu purchased by customer C is ramen.

---
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

````sql
SELECT
	TOP 1
	menu.product_name,
	COUNT (sales.product_id) AS total_purcashed
FROM dannys_dinner_db.dbo.sales
INNER JOIN dannys_dinner_db.dbo.menu
	ON menu.product_id = sales.product_id
GROUP BY menu.product_name
ORDER BY total_purcashed DESC;
````
### Steps
- Information about how many times each menu item is ordered by customers is in the __‘sales’__ table, and information about each menu item name is in the __‘menu’__ table, so combine the two tables with __INNER JOIN__.
- Count how many orders each menu item has using __COUNT__ and give the result a name, __‘total_purcashed’__.
- Sort the results of how many orders each menu item has received from most to least using __ORDER BY total_purchased__ with the __DESC__ clause.
- And since we only want to know the most purchased item on the menu, we can use the additional column __TOP 1__.

### Answer
- The most purchased item on the menu is ramen, which sells 8 times.

---
### 5. Which item was the most popular for each customer?

````sql
WITH popular_menu AS (
	SELECT
		sales.customer_id,
		menu.product_name,
		COUNT (sales.product_id) AS items,
		DENSE_RANK() OVER (
			PARTITION BY sales.customer_id
			ORDER BY COUNT (sales.product_id) DESC) AS ranking
	FROM dannys_dinner_db.dbo.sales
	INNER JOIN dannys_dinner_db.dbo.menu
		ON sales.product_id = menu.product_id
	GROUP BY sales.customer_id, menu.product_name
	)

	SELECT 
		customer_id,
		product_name,
		items
	FROM popular_menu
	WHERE ranking =1;
````
### Steps
- To find out the most popular rankings or menus, use CTE (Common Table Expression) or a temporary query to create rankings. And the temporary query is named 'popular_menu'.
- Data related to the number of menus ordered by customers is in the ‘sales’ table, and data regarding product names and IDs is in the ‘menu’ table.
- Combine the two tables using INNER JOIN based on product_id
- Sort the most ordered menus by adding a new column using DENSE_RANK(), where the data is partitioned based on sales.customer_id and sorted by the number of item purchases (COUNT(sales.product_id)) from largest to smallest
- The final query then filters the CTE result to retrieve only the most popular item per customer with the condition WHERE ranking = 1.

### Answer
- For Customer A, Ramen is the most popular items.
- For Customer B, there are several the most popular items, which are Curry, Sushi, Ramen.
- For Customer C, Ramen is the most popular items.

---
### 6. Which item was purchased first by the customer after they became a member?
