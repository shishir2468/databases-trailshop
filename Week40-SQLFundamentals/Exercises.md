# Week 40 — Exercises: SQL Fundamentals

> [!IMPORTANT]
> **_How to Complete These Exercises_**
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

## Exercise 1: TrailShop Project Task

This week you'll build the TrailShop database from scratch and practice manipulating data.

### Task 1.1: Create the Database

1. Open your PostgreSQL terminal (psql) or pgAdmin
2. Create a new database called `trailshop`
3. Connect to it

### Task 1.2: Create All Tables

Write and execute the CREATE TABLE statements for all six TrailShop tables in the correct order:

- categories
- customers
- products
- product_categories
- orders
- order_items

**Requirements:**

- Use appropriate data types for each column
- Include all constraints from the theory (NOT NULL, UNIQUE, CHECK, FOREIGN KEY, DEFAULT)
- Use SERIAL for primary keys
- Ensure foreign keys reference the correct parent tables

**Verify** by running `\dt` in psql to list all tables.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> (CREATE TABLE categories (
>    category_id  SERIAL PRIMARY KEY,
>   name         VARCHAR(100) NOT NULL UNIQUE,
>    description  TEXT);
> 
> CREATE TABLE customers (
>  customer_id  SERIAL PRIMARY KEY,
> first_name   VARCHAR(100) NOT NULL,
>   last_name    VARCHAR(100) NOT NULL
>   email        VARCHAR(255) NOT NULL UNIQUE,
>   created_at   TIMESTAMPTZ  NOT NULL DEFAULT CURRENT_TIMESTAMP);
>
> CREATE TABLE products (
>  product_id   SERIAL PRIMARY KEY,
>   name         VARCHAR(200) NOT NULL,
>   description  TEXT,
>   price        NUMERIC(10,2) NOT NULL CHECK (price > 0),
>   stock        INTEGER NOT NULL DEFAULT 0 CHECK (stock >= 0),
>  created_at   TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP);
>
> CREATE TABLE product_categories (
>   product_id  INTEGER NOT NULL REFERENCES products(product_id) ON DELETE CASCADE,
>   category_id INTEGER NOT NULL REFERENCES categories(category_id) ON DELETE CASCADE,
>   PRIMARY KEY (product_id, category_id)
> );
>
> CREATE TABLE orders (
>   order_id     SERIAL PRIMARY KEY,
>   customer_id  INTEGER NOT NULL REFERENCES customers(customer_id),
>   order_date   TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
>   status       VARCHAR(20) NOT NULL DEFAULT 'pending'
>                CHECK (status IN ('pending', 'shipped', 'delivered', 'cancelled'))
> );
>
> CREATE TABLE order_items (
>   order_item_id  SERIAL PRIMARY KEY,
>   order_id       INTEGER NOT NULL REFERENCES orders(order_id) ON DELETE CASCADE,
>   product_id     INTEGER NOT NULL REFERENCES products(product_id),
>   quantity       INTEGER NOT NULL CHECK (quantity > 0),
>   unit_price     NUMERIC(10,2) NOT NULL CHECK (unit_price > 0)
> );
> 

### Task 1.3: Insert Sample Data

Insert the following data:

**Categories** (at least 5):

- Footwear, Backpacks, Tents, Clothing, Accessories

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO categories (name, description) VALUES
    ('Footwear', 'Hiking boots, trail runners, and sandals'),
    ('Backpacks', 'Day packs, overnight packs, and expedition packs'),
    ('Tents', 'One-person to family-size tents'),
    ('Clothing', 'Outdoor clothing for all seasons'),
    ('Accessories', 'Water bottles, headlamps, trekking poles');
>
>
> ```

**Customers** (at least 5):

- Use easy to write names with realistic email addresses

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO customers (first_name, last_name, email) VALUES
    ('Anna', 'Virtanen', 'anna.v@email.com'),
    ('Mikko', 'Korhonen', 'mikko.k@email.com'),
    ('Sara', 'Mäkinen', 'sara.m@email.com'),
    ('Juha', 'Nieminen', 'juha.n@email.com'),
    ('Laura', 'Hämäläinen', 'laura.h@email.com');
>
>
> ```

**Products** (at least 10):

- At least 2 products per category
- At least one product assigned to **two or more** categories
- Prices ranging from €20 to €500
- Various stock levels

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO products (name, description, price, stock) VALUES
    ('TrailMaster X4', 'Professional hiking boot with Gore-Tex lining', 149.99, 25),
    ('LiteStep Pro', 'Lightweight trail runner for day hikes', 89.99, 40),
    ('Summit 45L', 'Multi-day hiking backpack with rain cover', 199.99, 15),
    ('DayTripper 20L', 'Compact day pack with hydration sleeve', 59.99, 50),
    ('CloudNest 2P', 'Two-person ultralight tent', 349.99, 10),
    ('StormShield 4P', 'Four-season family tent', 499.99, 5),
    ('ThermoLayer Jacket', 'Insulated mid-layer for cold weather', 129.99, 30),
    ('RainGuard Pro', 'Waterproof breathable rain jacket', 179.99, 20),
    ('HydroFlask 1L', 'Insulated stainless steel water bottle', 34.99, 100),
    ('LumaBeam 800', 'Rechargeable headlamp, 800 lumens', 44.99, 60);
>
>
> ```

**Product categories:**

- Insert rows into `product_categories` so every sample product is linked to at least one category

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO product_categories (product_id, category_id) VALUES
    (1, 1), (2, 1),
    (3, 2), (4, 2),
    (5, 3), (6, 3),
    (7, 4), (8, 4), (8, 5),
    (9, 5), (10, 5);
>
>
> ```

**Orders** (at least 5):

- Different customers, different statuses

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO orders (customer_id, status) VALUES
    (1, 'delivered'),
    (2, 'shipped'),
    (1, 'pending'),
    (3, 'delivered'),
    (4, 'pending');
>
>
> ```

**Order Items** (at least 10):

- Multiple items in some orders, single items in others

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO order_items (order_id, product_id, quantity, unit_price) VALUES
    (1, 1, 1, 149.99),
    (1, 9, 2, 34.99),
    (2, 3, 1, 199.99),
    (2, 7, 1, 129.99),
    (3, 5, 1, 349.99),
    (4, 2, 1, 89.99),
    (4, 4, 1, 59.99),
    (4, 10, 1, 44.99),
    (5, 6, 1, 499.99),
    (5, 8, 1, 179.99);
>
>
> ```

**Verify** each insert with `SELECT * FROM table_name;`

### Task 1.4: Practice UPDATE

> [!TIP]
> **Recommended practice.** Do Tasks 1.4–1.6. They are not required to finish the TrailShop project. They prepare you for the exams. Task 1.6 renames `stock` to `quantity_in_stock`. Later weeks still use `stock`, so after you practice the rename, change the column name back.

Perform the following updates and verify each one:

1. Increase the price of all products in the Footwear category by 10% (join through `product_categories`)
2. Change customer #3's email to a new address
3. Update the status of order #2 from 'shipped' to 'delivered'
4. Set the stock of 'HydroFlask 1L' to 85
5. Add a description to any product that currently has NULL in description

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your queries here
> -- 1. Increase price of all products in Footwear (category 1) by 10%
> UPDATE products
> SET price = price * 1.10
>WHERE product_id IN (SELECT product_id FROM product_categories WHERE category_id = 1 );
> -- 2. Change customer #3's email
> UPDATE customers
> SET email = 'sara.makinen.new@email.com'
> WHERE customer_id = 3;
> -- 3. Update the status of order #2
> UPDATE orders
> SET status = 'delivered'
> WHERE order_id = 2;
> -- 4. Set the stock of 'HydroFlask 1L' to 85
> UPDATE products
> SET stock = 85
> WHERE name = 'HydroFlask 1L';
> -- 5. Add a description to any product that has NULL
> UPDATE products
> SET description = 'Product description coming soon'
> WHERE description IS NULL;
>
>
> ```

### Task 1.5: Practice DELETE

1. Delete the most recently created order (and observe what happens to its order_items if you used CASCADE)
2. Try to delete a product that appears in `order_items` — what error do you get?
3. Delete a category that has products linked through `product_categories`. The products should remain; only the link rows should disappear. Confirm this.
4. Delete a customer who has no orders

>[!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your queries here
> -- 1. Delete the most recently created order (Assuming order_id 5)
> DELETE FROM orders WHERE order_id = 5;
>
> -- 2. Try to delete a product that appears in order_items
> -- (Throws ERROR: update or delete on table "products" violates foreign key constraint)
> DELETE FROM products WHERE product_id = 1;
>
> -- 3. Delete a category that has products linked
> DELETE FROM categories WHERE category_id = 1;
>
> -- 4. Delete a customer who has no orders
> DELETE FROM customers WHERE customer_id = 5;
>
>
> ```

---

### Task 1.6: Practice ALTER TABLE

1. Add a column `phone VARCHAR(20)` to the customers table
2. Add a column `weight_grams INTEGER` to the products table
3. Add a CHECK constraint to ensure `weight_grams > 0` (allow NULL though — not all products have weight recorded yet)
4. Rename the `stock` column in products to `quantity_in_stock`

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your queries here
> -- 1. Add a column phone
> ALTER TABLE customers ADD COLUMN phone VARCHAR(20);
>
> -- 2. Add a column weight_grams
> ALTER TABLE products ADD COLUMN weight_grams INTEGER;
>
> -- 3. Add a CHECK constraint to ensure weight_grams > 0
> ALTER TABLE products ADD CONSTRAINT chk_weight_positive CHECK (weight_grams > 0);
>
> -- 4. Rename the stock column to quantity_in_stock
> ALTER TABLE products RENAME COLUMN stock TO quantity_in_stock;
>
>
> ```

---

## Exercise 2: Theory Review Questions

Answer the following questions in your own words using the answer fields below:

1. What does SQL stand for, and why was the language designed to look like English?

> [!NOTE]
> **_Your Answer_**
>
> _(SQL stands for Structured Query Language. It was intentionally designed to resemble English sentences so that non-programmers could easily read, understand, and write queries to interact with relational databases without needing complex programming knowledge.)_
> 

2. Explain the difference between DDL and DML. Give two example commands for each.

> [!NOTE]
> **_Your Answer_**
>
> _(DDL (Data Definition Language) is used to define and manage the structure of the database and its objects (e.g., CREATE TABLE, DROP TABLE). DML (Data Manipulation Language) is used to interact with the data inside those structures, allowing you to add, edit, or remove rows (e.g., INSERT INTO, UPDATE).)_

3. What is the difference between DCL and TCL? When would you use each?

> [!NOTE]
> **_Your Answer_**
>
> _(DCL (Data Control Language) manages security and permissions (who can access or modify data). You use it when granting or revoking roles. TCL (Transaction Control Language) manages grouping operations into atomic transactions that must succeed or fail as a single unit. You use TCL (e.g., BEGIN, COMMIT, ROLLBACK) when executing multiple related DML statements that rely on each other to maintain data integrity.)_

4. Why must you create tables in a specific order? What determines that order?

> [!NOTE]
> **_Your Answer_**
>
> _(You must create tables in dependency order because a table cannot reference a foreign key constraint to a table that does not exist yet. The parent tables (those being referenced) must be created before the child tables (those making the reference).)_

5. What is the difference between a column-level constraint and a table-level constraint? When _must_ you use a table-level constraint?

> [!NOTE]
> **_Your Answer_**
>
> _(A column-level constraint is declared immediately after a column's data type, applying only to that specific column. A table-level constraint is declared after all columns are listed. You must use a table-level constraint when the constraint applies to multiple columns simultaneously, such as a composite primary key or a composite unique constraint.)_

6. Explain the difference between `DELETE FROM products;` and `TRUNCATE TABLE products;`. When would you prefer each?

> [!NOTE]
> **_Your Answer_**
>
> _(DELETE FROM products; scans and removes rows one-by-one, firing triggers and allowing rollbacks, making it slower but highly specific. TRUNCATE TABLE products; instantly drops all data in the table, resets sequences, and bypasses row-level triggers, making it extremely fast. Use DELETE to remove specific records based on conditions; use TRUNCATE when you need to completely empty a table quickly (e.g., resetting sample data).)_

7. What does `ON DELETE CASCADE` do on a foreign key? Give a real-world scenario where it's appropriate and one where it would be dangerous.

> [!NOTE]
> **_Your Answer_**
>
> _(ON DELETE CASCADE automatically deletes all referencing child rows when the parent row is deleted.
Appropriate: Deleting an order should cascade to automatically delete all order_items inside it.
Dangerous: Deleting a customer cascading to delete all their historical orders would destroy vital financial and transactional history.)_

8. Why should you store `unit_price` in the `order_items` table instead of just looking it up from the `products` table?

> [!NOTE]
> **_Your Answer_**
>
> _(You must store the unit_price in order_items to record the exact price paid at the time of the transaction. If you relied solely on the products table, updating a product's price in the future would retroactively alter the financial totals of all past orders.)_

9. What is the difference between SERIAL and GENERATED ALWAYS AS IDENTITY? Which would you use in a new project and why?

> [!NOTE]
> **_Your Answer_**
>
> _(SERIAL is a legacy, PostgreSQL-specific shorthand that creates an integer and a sequence, but allows users to easily override the ID manually. GENERATED ALWAYS AS IDENTITY is the modern SQL standard (SQL:2003) which prevents accidental manual inserts unless explicitly overridden. For a new project, use GENERATED ALWAYS AS IDENTITY because it enforces stricter data integrity and is portable across different database systems.)_

10. Explain why `UPDATE products SET price = 9.99;` is dangerous. What steps should you take before running any UPDATE statement?

> [!NOTE]
> **_Your Answer_**
>
> _(It is dangerous because it lacks a WHERE clause, meaning it will blindly overwrite the price of every single product in the database to 9.99. Before running an UPDATE, you should: 1) Write the WHERE clause first; 2) Run a SELECT with that same WHERE clause to verify exactly which rows will be affected; and 3) Wrap the update in a BEGIN; and COMMIT; transaction to allow a ROLLBACK if a mistake happens.)_

---

## Exercise 3: SQL Writing Exercises (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

Write the SQL statements for each task in the **Your SQL** fields below. Verify by running them when ready.

### 3.1 CREATE TABLE

Write a CREATE TABLE statement for a `suppliers` table with the following columns:

- supplier_id (auto-incrementing primary key)
- company_name (required, max 200 characters, must be unique)
- contact_name (max 150 characters)
- email (max 255 characters, required, unique)
- phone (max 20 characters)
- country (max 100 characters, required, default 'Finland')

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> CREATE TABLE suppliers (
    supplier_id   SERIAL PRIMARY KEY,
    company_name  VARCHAR(200) NOT NULL UNIQUE,
    contact_name  VARCHAR(150),
    email         VARCHAR(255) NOT NULL UNIQUE,
    phone         VARCHAR(20),
    country       VARCHAR(100) NOT NULL DEFAULT 'Finland'
);
>
>
> ```

### 3.2 CREATE TABLE with Foreign Key

Write a CREATE TABLE statement for a `product_reviews` table:

- review_id (auto-incrementing primary key)
- product_id (required, references products)
- customer_id (required, references customers)
- rating (required integer, must be between 1 and 5 inclusive)
- review_text (optional, unlimited length)
- created_at (required, defaults to current timestamp)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> CREATE TABLE product_reviews (
    review_id    SERIAL PRIMARY KEY,
    product_id   INTEGER NOT NULL REFERENCES products(product_id),
    customer_id  INTEGER NOT NULL REFERENCES customers(customer_id),
    rating       INTEGER NOT NULL CHECK (rating >= 1 AND rating <= 5),
    review_text  TEXT,
    created_at   TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);
>
>
> ```

### 3.3 INSERT — Single Row

Write an INSERT statement to add a new category called 'Electronics' with description 'GPS devices, solar chargers, and tech gear'.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO categories (name, description)
> VALUES ('Electronics', 'GPS devices, solar chargers, and tech gear');
>
>
> ```

### 3.4 INSERT — Multiple Rows

Write a single INSERT statement that adds three new customers:

- Eero Lahtinen, eero.l@email.com
- Maria Salminen, maria.s@email.com
- Petri Kallio, petri.k@email.com

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> INSERT INTO customers (first_name, last_name, email) VALUES
    ('Eero', 'Lahtinen', 'eero.l@email.com'),
    ('Maria', 'Salminen', 'maria.s@email.com'),
    ('Petri', 'Kallio', 'petri.k@email.com');
>
>
> ```

### 3.5 INSERT with RETURNING

Write an INSERT statement that adds a new product called 'NorthStar GPS' priced at €229.99 with stock of 12, then assign it to category 'Electronics' (assume `category_id = 6`) using `product_categories`. Return the product_id and created_at.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
> -- Step 1: Insert the product and return its details
> INSERT INTO products (name, price, stock)
> VALUES ('NorthStar GPS', 229.99, 12)
> RETURNING product_id, created_at;
>
> -- Step 2: Assign it to the Electronics category
> -- (Replace 11 with the product_id returned from Step 1)
> INSERT INTO product_categories (product_id, category_id)
VALUES (11, 6);
>
>
> ```

### 3.6 UPDATE — Simple

Write an UPDATE statement that changes the email of the customer with customer_id = 2 to 'mikko.korhonen@newmail.com'.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.7 UPDATE — Expression

Write an UPDATE statement that reduces the stock of all products by 1 where the stock is currently greater than 0.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.8 UPDATE — Multiple Columns

Write an UPDATE statement that changes order #3 to status 'cancelled' and sets a (hypothetical) cancelled_at timestamp to the current time. (Assume you've already added a cancelled_at column.)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.9 DELETE — With Condition

Write a DELETE statement that removes all orders with status 'cancelled'.

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

### 3.10 ALTER TABLE

Write the ALTER TABLE statements to:
a) Add a `discount_percent NUMERIC(5,2) DEFAULT 0 CHECK (discount_percent >= 0 AND discount_percent <= 100)` column to products
b) Drop the `description` column from categories
c) Add a composite unique constraint on (customer_id, product_id) in the product_reviews table (preventing a customer from reviewing the same product twice)

> [!NOTE]
> **_Your SQL_**
>
> ```sql
> -- Write your query here
>
>
> ```

---

## Exercise 4: Error Diagnosis (Optional)

> [!TIP]
> **Recommended practice.** Do this section. It is not required to finish the TrailShop project. It prepares you for the exams.

Each of the following SQL statements contains one or more errors. Identify the error(s) and write the corrected version.

### 4.1

```sql
CREATE TABLE warehouses
    warehouse_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    city VARCHAR(100
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.2

```sql
INSERT INTO products (name, price, stock)
VALUES ("Alpine Sleeping Bag", 89.99, 20);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.3

```sql
CREATE TABLE shipments (
    shipment_id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(order_id)
    shipped_date DATE NOT NULL,
    carrier VARCHAR(100)
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.4

```sql
UPDATE products
SET price = price * 0.9
SET stock = stock + 10
WHERE product_id = 3;
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

### 4.5

```sql
CREATE TABLE wishlists (
    wishlist_id SERIAL PRIMARY KEY,
    customer_id INTEGER NOT NULL REFERENCES customers(customer_id),
    product_id INTEGER NOT NULL REFERENCES products(product_id),
    added_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (customer_id, product_id)
);
```

> [!NOTE]
> **_Error(s) Identified_**
>
> _(Describe what is wrong.)_

> [!NOTE]
> **_Corrected SQL_**
>
> ```sql
> -- Write the corrected statement here
>
>
> ```

---

## Submission Checklist

**Required**

- [ ] All 6 TrailShop tables created successfully
- [ ] Sample data inserted (at least 5 categories, 5 customers, 10 products, product_categories links, 5 orders, 10 order items)
- [ ] Theory review questions answered

**Recommended practice**

- [ ] UPDATE exercises completed and verified
- [ ] DELETE exercises completed and verified
- [ ] ALTER TABLE exercises completed, then `quantity_in_stock` renamed back to `stock`
- [ ] SQL writing exercises completed
- [ ] Error diagnosis completed with corrections
