# 1️⃣ Case Study #1 - Danny's Diner
- Dataset: Case Study #1 - Danny's Diner  
- Source: DB Fiddle https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138  
- Queried using: MySQL Workbench  

***

## Database Schema
This case study uses three tables:
- `sales` : The sales table captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.
- `menu` : The menu table maps the product_id to the actual product_name and price of each menu item.
- `members` : The final members table captures the join_date when a customer_id joined the beta version of the Danny’s Diner loyalty program.
The database schema for this case study can be found here:
👉 [schema.sql](schema.sql)

***

## Entity Relationship Diagram
<img width="1080" height="525" alt="image" src="https://github.com/user-attachments/assets/b7dc080c-ed6e-44b2-a5d9-9aa2dc211170" />

---

