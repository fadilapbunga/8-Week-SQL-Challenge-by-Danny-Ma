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
__1. What is the total amount each customer spent at the restaurant?__

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

