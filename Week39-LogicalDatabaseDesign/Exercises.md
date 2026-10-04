# Week 39 — Logical Database Design: Exercises

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 39 Theory material. Refer to the theory sections indicated in brackets when you need help.

---

## Exercise 1: TrailShop Project Task — Build the Schema

**Goal:** Convert the TrailShop ER diagram (from Week 38) into a complete PostgreSQL relational schema.

### Instructions

Write `CREATE TABLE` statements for all six TrailShop tables:

1. `categories`
2. `customers`
3. `products`
4. `product_categories`
5. `orders`
6. `order_items`

### Requirements

For each table, you must:

- Choose appropriate PostgreSQL data types for every column (justify at least 3 choices in writing)
- Define primary keys (surrogate or composite as appropriate)
- Define foreign keys with explicit `ON DELETE` and `ON UPDATE` actions (justify each choice)
- Add `NOT NULL`, `UNIQUE`, `CHECK`, and `DEFAULT` constraints where appropriate
- Create tables in the correct dependency order
- Follow the naming conventions from Theory Section 8

### Deliverables

1. A single `.sql` file with all six `CREATE TABLE` statements (executable in PostgreSQL)
2. A short justification for data types, FK actions and design decisions:
   - Justification for 3 data type choices (e.g., why `NUMERIC(10,2)` for price instead of `REAL`)
   - Justification for each FK action choice (e.g., why CASCADE on `order_items.order_id`)
   - One design decision you made that wasn't specified in the requirements (e.g., whether shipping address is optional)

### Bonus Challenge

After creating the tables, insert sample data:
- At least 5 categories
- At least 8 products (across at least 3 categories)
- At least one product assigned to **two or more** categories via `product_categories`
- At least 3 customers
- At least 4 orders (across at least 2 customers)
- At least 10 order items

Verify that your constraints work by attempting at least 2 invalid inserts and showing the error messages.

> [!NOTE]
> ***Your SQL***
>
> ```sql

-- 1. Categories
CREATE TABLE categories (
    category_id   SERIAL       PRIMARY KEY,
    category_name VARCHAR(50)  NOT NULL UNIQUE,
    description   TEXT
);

-- 2. Customers
CREATE TABLE customers (
    customer_id   SERIAL        PRIMARY KEY,
    first_name    VARCHAR(50)   NOT NULL,
    last_name     VARCHAR(50)   NOT NULL,
    email         VARCHAR(254)  NOT NULL UNIQUE,
    phone         VARCHAR(20),
    street        VARCHAR(100)  NOT NULL,
    city          VARCHAR(50)   NOT NULL,
    postal_code   VARCHAR(10)   NOT NULL,
    country       VARCHAR(50)   NOT NULL DEFAULT 'Finland',
    registered_at TIMESTAMPTZ   NOT NULL DEFAULT NOW()
);

-- 3. Products
CREATE TABLE products (
    product_id     SERIAL         PRIMARY KEY,
    name           VARCHAR(100)   NOT NULL,
    description    TEXT,
    price          NUMERIC(10,2)  NOT NULL CHECK (price > 0),
    weight_kg      NUMERIC(6,2)   CHECK (weight_kg > 0),
    stock_quantity INTEGER        NOT NULL DEFAULT 0 CHECK (stock_quantity >= 0),
    created_at     TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

-- 4. Product Categories (Junction Table for M:N)
CREATE TABLE product_categories (
    product_id  INTEGER NOT NULL REFERENCES products(product_id) ON DELETE CASCADE,
    category_id INTEGER NOT NULL REFERENCES categories(category_id) ON DELETE CASCADE,
    PRIMARY KEY (product_id, category_id)
);

-- 5. Orders
CREATE TABLE orders (
    order_id    SERIAL       PRIMARY KEY,
    customer_id INTEGER      NOT NULL REFERENCES customers(customer_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    order_date  TIMESTAMPTZ  NOT NULL DEFAULT NOW(),
    status      VARCHAR(20)  NOT NULL DEFAULT 'pending' 
                CHECK (status IN ('pending', 'processing', 'shipped', 'delivered', 'cancelled')),
    shipping_street      VARCHAR(100),
    shipping_city        VARCHAR(50),
    shipping_postal_code VARCHAR(10),
    shipping_country     VARCHAR(50)
);

-- 6. Order Items
CREATE TABLE order_items (
    order_id   INTEGER        NOT NULL REFERENCES orders(order_id) ON DELETE CASCADE ON UPDATE CASCADE,
    product_id INTEGER        NOT NULL REFERENCES products(product_id) ON DELETE RESTRICT ON UPDATE CASCADE,
    quantity   INTEGER        NOT NULL CHECK (quantity > 0),
    unit_price NUMERIC(10,2)  NOT NULL CHECK (unit_price > 0),
    PRIMARY KEY (order_id, product_id)
);

-- ==========================================
-- BONUS CHALLENGE: DUMMY DATA
-- ==========================================

INSERT INTO categories (category_name, description) VALUES
('Footwear', 'Hiking boots and trail shoes'),
('Camping', 'Tents, sleeping bags, and camp gear'),
('Apparel', 'Jackets, pants, and base layers'),
('Backpacks', 'Daypacks and multi-day expedition packs'),
('Accessories', 'Headlamps, water bottles, and navigation');

INSERT INTO customers (first_name, last_name, email, phone, street, city, postal_code) VALUES
('Matti', 'Meikäläinen', 'matti@example.fi', '0401234567', 'Mannerheimintie 1', 'Helsinki', '00100'),
('Anna', 'Virtanen', 'anna.v@example.fi', '0509876543', 'Kauppakatu 5', 'Tampere', '33100'),
('Mikko', 'Lahtinen', 'mikko.l@example.fi', NULL, 'Aleksanterinkatu 10', 'Oulu', '90100');

INSERT INTO products (name, description, price, weight_kg, stock_quantity) VALUES
('Alpine Pro Boots', 'Waterproof hiking boots', 189.99, 1.20, 15),
('TrailMaster X4 Tent', '4-person 3-season tent', 249.50, 3.50, 8),
('Gore-Tex Shell Jacket', 'Lightweight rain jacket', 120.00, 0.45, 20),
('Merino Wool Base Layer', 'Warm breathable top', 65.00, 0.25, 30),
('Summit 65L Backpack', 'Expedition backpack', 199.00, 2.10, 12),
('LED Headlamp', '500 lumen rechargeable', 45.00, 0.15, 40),
('Titanium Spork', 'Ultralight camping utensil', 12.50, 0.02, 100),
('Insulated Water Bottle', 'Keeps water cold for 24h', 35.00, 0.40, 50);

-- 1 product assigned to 2 categories (Headlamp in Accessories and Camping)
INSERT INTO product_categories (product_id, category_id) VALUES
(1, 1), (2, 2), (3, 3), (4, 3), (5, 4), (6, 5), (6, 2), (7, 2), (8, 5);

INSERT INTO orders (customer_id, status) VALUES
(1, 'delivered'), (1, 'processing'), (2, 'shipped'), (2, 'pending');

INSERT INTO order_items (order_id, product_id, quantity, unit_price) VALUES
(1, 1, 1, 189.99), (1, 6, 1, 45.00), (1, 8, 2, 35.00),
(2, 3, 1, 120.00), (2, 4, 2, 65.00),
(3, 2, 1, 249.50), (3, 7, 4, 12.50),
(4, 5, 1, 199.00), (4, 6, 1, 45.00), (4, 8, 1, 35.00);

-- ==========================================
-- BONUS CHALLENGE: INVALID INSERTS (CONSTRAINTS TEST)
-- ==========================================
-- Test 1: Fails the CHECK constraint (price > 0)
-- INSERT INTO products (name, price, stock_quantity) VALUES ('Free Tent', -15.00, 5);
-- ERROR: new row for relation "products" violates check constraint "products_price_check"

-- Test 2: Fails the UNIQUE constraint (email already exists)
-- INSERT INTO customers (first_name, last_name, email, street, city, postal_code) 
-- VALUES ('Evil', 'Twin', 'matti@example.fi', 'Fake St', 'Helsinki', '00100');
-- ERROR: duplicate key value violates unique constraint "customers_email_key"

>
>
> ```

> [!NOTE]
> ***Your Answer***
>
> *

Data Type Justifications:
1. NUMERIC(10,2) for price: Floating-point types like REAL can cause rounding errors. NUMERIC guarantees exact decimal precision, which is mandatory for financial data.
2. VARCHAR(254) for email: The official internet standard (RFC 5321) defines 254 characters as the maximum valid length for an email address. This limit protects the database from excessively long junk inputs.
3. TIMESTAMPTZ for dates: Timestamp with Time Zone saves the exact moment universally (in UTC). This prevents data inconsistencies regardless of where a customer or server is located.

   
Foreign Key Action Justifications:
- ON DELETE CASCADE (from order_items and product_categories): If an order is deleted, its line items must be deleted automatically because they are weak entities that cannot exist without the parent order. The same applies to junction tables.
- ON DELETE RESTRICT (from orders to customers, and order_items to products): This prevents a customer account or a product from being deleted if they have a history of orders. It strictly protects the integrity of the business's sales and financial records.

  
Design Decision:
- I made the shipping_street, shipping_city, shipping_postal_code, and shipping_country columns in the orders table optional (nullable). The business logic dictates that if these fields are left blank, the system will default to shipping the items to the primary address stored in the customers table.


*
>
>
>
>

## Exercise 2: Theory Review Questions

Answer each question in 2–4 sentences. Reference the relevant theory section.

1. List the seven phases of the database development lifecycle in order. Which phase is this week's focus? *(Section 1)*

> [!NOTE]
> ***Your Answer***
>
> *

The seven phases are Requirements Gathering, Conceptual Design, Logical Design, Physical Design, Implementation, Testing & Validation, and Maintenance & Evolution. This week's focus is Logical Design, where we translate the conceptual model into a relational schema.
*
>
>
>
>

2. Explain the transformation rule for mapping a 1:N relationship to the relational model. Why is the foreign key placed on the "many" side? *(Section 3.2)*

> [!NOTE]
> ***Your Answer***
>
> *
A 1:N relationship is mapped by placing the primary key of the "one" side as a foreign key in the "many" side table. This is done to maintain data atomicity; each child only has one parent to reference, whereas putting the key on the "one" side would require awkwardly storing multiple IDs in a single column.
*
>
>
>
>

3. What is a junction table? When is it needed? Give an example not from TrailShop. *(Section 3.3)*

> [!NOTE]
> ***Your Answer***
>
> *

A junction table is a new table created to resolve a Many-to-Many (M:N) relationship by storing the primary keys of both participating tables as foreign keys. For example, in a university database, a student_courses junction table would be needed to link multiple students to multiple courses.
*
>
>
>
>

4. When mapping a 1:1 relationship, how do you decide which table gets the foreign key? *(Section 3.4)*

> [!NOTE]
> ***Your Answer***
>
> *
If one side has mandatory participation and the other is optional, place the foreign key on the mandatory side so it always has a value. If both are mandatory or both optional, place it on the side that makes SQL queries feel more natural or is more likely to be populated.
*
>
>
>
>

5. How does the mapping of a weak entity differ from a strong entity? What happens to the primary key? *(Section 3.5)*

> [!NOTE]
> ***Your Answer***
>
> *

A strong entity gets a standard table with its own independent primary key. A weak entity gets a table that must include its parent's primary key as a foreign key; furthermore, this foreign key is combined with the weak entity's partial key to form a composite primary key.
*
>
>
>
>

6. Why should you never use `REAL` or `DOUBLE PRECISION` for monetary values? What should you use instead? *(Section 4.1)*

> [!NOTE]
> ***Your Answer***
>
> *


REAL and DOUBLE PRECISION are floating-point types that store approximations, leading to rounding errors in calculations (e.g., 0.1 + 0.2 = 0.30000001). You should always use exact decimal types like NUMERIC(p,s) to guarantee accurate financial math.
*
>
>
>
>

7. What is the difference between `TIMESTAMP` and `TIMESTAMPTZ`? Which should you prefer and why? *(Section 4.3)*
> [!NOTE]
> ***Your Answer***
>
> *

TIMESTAMP stores the date and time exactly as entered without any timezone awareness. You should prefer TIMESTAMPTZ because it stores the exact moment in UTC and automatically converts it to the user's local timezone on display, preventing timezone mismatch bugs.
*
>




8. Explain the difference between `CASCADE` and `RESTRICT` as foreign key delete actions. Give a scenario where each is appropriate. *(Section 6)*
> [!NOTE]
> ***Your Answer***
>
> *

CASCADE automatically deletes child rows when the parent is deleted (e.g., deleting an order automatically deletes its order items). 
RESTRICT blocks the deletion of the parent if child rows exist, protecting data integrity (e.g., blocking the deletion of a product if it is tied to historical order items).
*
>




9. What is an insertion anomaly? Give an example and explain how proper schema design prevents it. *(Section 7)*
> [!NOTE]
> ***Your Answer***
>
> *
An insertion anomaly occurs when you cannot insert data without simultaneously inserting unrelated data. For example, if categories and products share one table, you cannot add a new "Cycling" category without also inserting a dummy cycling product. Normalization (separating into two tables) prevents this.
*
>




10. What is the difference between a surrogate key and a natural key? Give one advantage of each. *(Section 9)*
> [!NOTE]
> ***Your Answer***
>
> *

A natural key is a column with real-world meaning, like an email address, which is advantageous because it natively prevents real-world duplicates. A surrogate key is an artificial integer (like SERIAL) with no business meaning; its advantage is that it provides fast JOIN performance and never changes.
*
>




11. Why does PostgreSQL fold unquoted identifiers to lowercase? How does `snake_case` naming help? *(Section 8)*

> [!NOTE]
> ***Your Answer***
>
> *
PostgreSQL automatically folds unquoted identifiers to lowercase, meaning OrderItems becomes orderitems. snake_case (like order_items) helps because it preserves word separation without requiring you to wrap your table names in double quotes every time you write a query.
*
>
>
>
>

12. What does `SET NULL` do as a foreign key action? When would you use it instead of `CASCADE`? *(Section 6)*
> [!NOTE]
> ***Your Answer***
>
> *

SET NULL changes the child's foreign key value to NULL when the parent record is deleted, rather than deleting the child record entirely. You use this when the child should survive independently but simply lose its link, such as deleting a manager but keeping the employees in the system.
*
>




---

## Exercise 3: Transformation Exercise — Hotel Booking System

### Given ER Diagram

A hotel booking system has the following entities and relationships:

**Entities:**

1. **Hotel** — hotel_id (PK), name, city, star_rating, phone
2. **Room** (weak entity, owned by Hotel) — room_number (partial key), room_type, floor, price_per_night, has_balcony
3. **Guest** — guest_id (PK), first_name, last_name, email, phone, passport_number
4. **Booking** — booking_id (PK), check_in_date, check_out_date, total_amount, status
5. **Service** — service_id (PK), name, description, price (e.g., "Room Service", "Spa", "Airport Shuttle")

**Relationships:**

- Hotel (1) → Room (N): A hotel has many rooms. Each room belongs to exactly one hotel. (Identifying relationship — Room is weak.)
- Guest (1) → Booking (N): A guest can make many bookings. Each booking belongs to one guest.
- Booking (M) ↔ Room (N): A booking can include multiple rooms, and a room can appear in many bookings (over time). The junction records the specific dates.
- Booking (M) ↔ Service (N): A booking can use multiple services, and a service can be used by many bookings. The junction records the date used and quantity.

### Task

1. Write `CREATE TABLE` statements for ALL tables (including junction tables).
2. For each table:
   - Choose appropriate data types
   - Define PK, FK, NOT NULL, UNIQUE, CHECK, and DEFAULT constraints
   - Specify ON DELETE and ON UPDATE actions for all FKs
3. Create the tables in the correct dependency order.
4. Explain why Room is a weak entity and how its PK reflects this.

> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write your CREATE TABLE statements here
>
>
> ```

> [!NOTE]
> ***Your Answer***
>
> *(Explain why Room is a weak entity and how its PK reflects this.)*
>
>
>
>

---

## Exercise 4: Data Type Selection

> [!NOTE]
> ***Your Answers***
> Fill in the **Your Data Type** and **Justification** columns in the table below.
>

For each column described below, choose the best PostgreSQL data type and write a brief justification (1–2 sentences). Do NOT just pick `VARCHAR` or `TEXT` for everything — think carefully about validation, storage, and query needs.

| # | Column Description | Your Data Type | Justification |
|---|---|---|---|
| 1 | Employee salary (exact, up to €999,999.99) | | |
| 2 | Number of items in stock (never negative, max ~50,000) | | |
| 3 | Whether a user's email is verified | | |
| 4 | Customer's date of birth | | |
| 5 | Product description (variable length, could be several paragraphs) | | |
| 6 | Country code (always exactly 2 letters, like "FI", "US") | | |
| 7 | IP address of a login attempt | | |
| 8 | Order total (exact, up to €9,999,999.99) | | |
| 9 | GPS latitude of a store location | | |
| 10 | A unique identifier for API tokens that must be globally unique across distributed systems | | |
| 11 | Duration of a video in seconds (always a whole number) | | |
| 12 | Timestamp of when a record was last modified (users in multiple time zones) | | |
| 13 | A Finnish phone number like "+358 40 123 4567" | | |
| 14 | A percentage discount (0.00% to 100.00%) | | |
| 15 | A product's color options (e.g., a product comes in "red", "blue", "green") | | |

---

## Exercise 5: Constraint Design

For each business rule below, write the appropriate PostgreSQL constraint. Provide the constraint as it would appear inside a `CREATE TABLE` statement or as an `ALTER TABLE` statement.

### Part A: Single-Column Constraints

1. "A product's weight must be greater than zero (if provided)."

2. "Every customer must have an email address."

3. "Product names must be unique."

4. "An employee's hire date defaults to today if not specified."

5. "Order status can only be one of: 'new', 'confirmed', 'shipped', 'delivered', 'returned'."

> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write constraints 1–5 here
>
>
> ```

### Part B: Multi-Column Constraints

6. "A flight's arrival time must be after its departure time."

7. "In the `enrollments` table, the combination of `student_id` and `course_id` must be unique (a student can only enroll in a course once)."

8. "A discount percentage must be between 0 and 100, inclusive."

> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write constraints 6–8 here
>
>
> ```

### Part C: Foreign Key Constraints with Actions

9. "When a department is deleted, all employees in that department should have their `department_id` set to NULL (they become unassigned)."

10. "When a customer is deleted, prevent the deletion if the customer has any orders."

11. "When an author is deleted, all their blog posts should be deleted automatically."

12. "When a course is deleted, all enrollments for that course should be removed."

> [!NOTE]
> ***Your SQL***
>
> ```sql
> -- Write constraints 9–12 here
>
>
> ```

---

## Submission Checklist

- [x ] Exercise 1: `.sql` file with all CREATE TABLE statements + written justifications
- [x ] Exercise 2: All 12 theory review answers
- [ ] Exercise 3: Hotel booking schema with all tables and explanations
- [ ] Exercise 4: Data type selections with justifications for all 15 columns
- [ ] Exercise 5: All 12 constraints written in valid PostgreSQL syntax
