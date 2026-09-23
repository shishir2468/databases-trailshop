# Week 38 — Conceptual Data Modelling: Exercises

> [!IMPORTANT]
> ***How to Complete These Exercises***
> Write your answers directly in the highlighted **Your Answer** / **Your SQL** fields below each task. Replace the placeholder text with your own work before submitting.

These exercises accompany the Week 38 Theory material. Refer to the theory sections indicated in brackets when you need help.

---

## Exercise 1: TrailShop Project Task — Create the ER Diagram

**Goal:** Create a complete Entity-Relationship diagram for the TrailShop database using crow's foot notation.

> **From Week 37:** Last week each product had a single `category_id` (Category 1:N Product). That cannot store a product in two categories. This week's diagram must **not** put `category_id` on Product. Use **ProductCategory** as the junction that resolves Category M:N Product (see Theory Section 1.4).

### Instructions

Using the entity descriptions from Theory Section 12, create an ER diagram that includes:

1. **All six entities**: Category, Product, ProductCategory, Customer, Order, OrderItem
2. **All attributes** for each entity (as listed in Section 12.1)
3. **Primary keys** clearly marked (underline or "PK" label)
4. **Foreign keys** clearly marked (dashed underline or "FK" label)
5. **Relationships** between entities with:
   - Relationship name (verb)
   - Crow's foot notation showing cardinality and participation
6. **Identify weak / junction entities** — mark OrderItem as a weak entity, and mark ProductCategory as the junction that resolves Category M:N Product. Do **not** draw a direct M:N line between Category and Product.

### Requirements

- Use crow's foot notation (see Theory Section 9)
- You may use any tool: draw.io, Lucidchart, ERDPlus, dbdiagram.io, or even pen and paper (photograph and submit)
- The diagram must be readable — avoid crossing lines where possible
- Include a brief legend explaining your notation if using pen and paper

### Deliverables

- The ER diagram (image or link to online tool)
- A short written paragraph (3–5 sentences) that **must** explain why Week 37's 1:N `products.category_id` is being replaced by ProductCategory. You may also discuss another design decision (for example why OrderItem is a weak entity, or why `unit_price` is stored in OrderItem).

> [!NOTE]
> ***Your Answer***
>
> *(Week 37's 1:N category_id attribute on the Product entity is replaced by the ProductCategory junction entity because a single product (like a rain jacket) can logically belong to multiple categories (e.g., "Clothing" and "Accessories"). Storing multiple category IDs in a single column violates atomicity, so the M:N relationship must be resolved using a junction table. Additionally, OrderItem is modeled as a weak, attributed junction entity because it depends entirely on Order for its existence, and it must store the unit_price at the time of purchase to ensure historical order totals remain accurate even if the main product price changes.

ER Diagram (Mermaid Notation):
https://imgur.com/a/DEK9TME

)*
>
>
>
>

---

## Exercise 2: Theory Review Questions

Answer each question in 2–4 sentences. Reference the relevant theory section. Question 11b is extra: it connects last week's 1:N category FK to this week's junction.

1. Why should you create a conceptual data model before writing SQL? Give two specific reasons. *(Section 1)*

> [!NOTE]
> ***Your Answer***
>
> *(Creating a conceptual data model prevents expensive, risky database restructuring after production data is loaded. It also serves as a technology-independent communication tool to validate business requirements with stakeholders before committing to physical table structure)*
>
>
>
>

2. What is the difference between the conceptual level and the logical level of a data model? *(Section 2)*

> [!NOTE]
> ***Your Answer***
>
> *(The conceptual level is a unified, technology-independent view of what data exists, how it relates, and its governing business rules. The logical level translates this conceptual model into the specific structures of a database system (like relational tables, columns, and foreign keys) without tying it to a specific software product.)*
>
>
>
>

3. Explain logical data independence with an example. *(Section 3)*

> [!NOTE]
> ***Your Answer***
>
> *(Logical data independence is the ability to change the conceptual schema without breaking the external user schemas (views). For example, if you split a products table into products and product_details for normalization, you can update the user view to JOIN these tables so the end-user or application experiences no disruption.)*
>
>
>
>

4. Explain physical data independence with an example. *(Section 3)*

> [!NOTE]
> ***Your Answer***
>
> *(Physical data independence is the ability to alter internal storage mechanics without changing the conceptual or external models. An example is adding a B-tree index to a products.name column to speed up search queries, or migrating the database to a faster storage drive, neither of which requires altering SQL queries.)*
>
>
>
>

5. What is the difference between a strong entity and a weak entity? Give one example of each (not from TrailShop). *(Section 5)*

> [!NOTE]
> ***Your Answer***
>
> *(A strong entity can be uniquely identified by its own primary key and exists independently, such as a Building with a building_id. A weak entity relies on a strong (owner) entity for identification, such as a Room with a room_number that needs the building_id to be globally unique.)*
>
>
>
>

6. What is a composite attribute? How does it differ from a multivalued attribute? Give an example of each. *(Section 6)*

> [!NOTE]
> ***Your Answer***
>
> *(A composite attribute can be broken down into smaller, meaningful sub-attributes, such as breaking full_name into first_name and last_name. A multivalued attribute holds multiple distinct values for a single entity instance, such as a person having multiple phone_numbers.)*
>
>
>
>

7. What is a derived attribute? Why is it usually not stored in the database? *(Section 6)*

> [!NOTE]
> ***Your Answer***
>
> *(A derived attribute is a value calculated from other existing attributes, such as calculating age from date_of_birth. It is usually not stored physically to avoid data inconsistency; if the underlying data changes, the stored derived value becomes instantly outdated unless manually updated.)*
>
>
>
>

8. Explain the difference between a binary relationship and a unary (recursive) relationship. Give an example of each. *(Section 7)*
> [!NOTE]
> ***Your Answer***
>
> *(A binary relationship links two different entity types, such as a Customer placing an Order. A unary (recursive) relationship links an entity type to itself, such as an Employee acting as a manager to another Employee.)*
>




9. What is the difference between an identifying relationship and a non-identifying relationship? How does this affect the child table's primary key? *(Section 7)*
> [!NOTE]
> ***Your Answer***
>
> *(In an identifying relationship, the child is a weak entity, and the parent's foreign key becomes a part of the child's composite primary key. In a non-identifying relationship, the child entity is independent, and the foreign key from the parent is just a regular column, not part of the primary key.)*
>




10. In crow's foot notation, what does the following endpoint mean: a circle followed by a crow's foot (fork)? *(Section 9)*

> [!NOTE]
> ***Your Answer***
>
> *(A circle followed by a crow's foot (──O<──) indicates "zero or many." It means participation is optional (minimum zero) and the maximum cardinality is many.)*
>
>
>
>

11. Why can't a many-to-many (M:N) relationship be directly implemented in a relational database? What is the solution? *(Section 10)*

> [!NOTE]
> ***Your Answer***
>
> *(Directly implementing an M:N relationship requires storing multiple foreign key values in a single cell, which violates the atomicity rule of first normal form. The solution is creating a junction (associative) table that sits between the two entities, holding foreign keys that reference both.)*
>
>
>
>

11b. Last week TrailShop used `products.category_id` so each product belonged to exactly one category. Why is that insufficient, and what ER construct replaces it? *(Section 1.4)*

> [!NOTE]
> ***Your Answer***
>
> *(It is insufficient because a single product often falls under multiple categories logically (e.g., a tent being both "Shelter" and "Summer Gear"). It is replaced by a many-to-many (M:N) relationship using a junction entity called ProductCategory.)*
>
>
>
>

12. A business rule states: "Every employee must belong to exactly one department, and every department must have at least one employee." Express this using min-max notation for both sides. *(Section 8)*

> [!NOTE]
> ***Your Answer***
>
> *(Employee (1,1) ──── belongs_to ──── (1,N) Department.)*
>
>
>
>

---

## Exercise 3: ER Diagram Reading Exercise

### Diagram A: Library System

Study the following ER description and answer the questions below.

```
┌──────────┐                        ┌──────────┐
│  AUTHOR  │──||──────O<────────────│   BOOK   │
└──────────┘                        └─────┬────┘
                                          │
                                    ||    │
                                          │
                                    O<    │
                                          │
                                   ┌──────┴─────┐
                                   │    LOAN     │
                                   └──────┬──────┘
                                          │
                                    ||    │
                                          │
                                    O<    │
                                          │
                                   ┌──────┴──────┐
                                   │   MEMBER    │
                                   └─────────────┘
```

Relationships (in crow's foot):
- Author `──||──────O<──` Book
- Book `──||──────O<──` Loan
- Member `──||──────O<──` Loan

**Questions:**

a) Can an author exist without having written any books? Explain using the notation.
> [!NOTE]
> ***Your Answer***
>
> *(Yes. The endpoint attached to the BOOK entity is ──O<── (circle and crow's foot), meaning the minimum cardinality is zero (optional).)*
>
>
>
>

b) Can a book exist without being loaned? Explain using the notation.
> [!NOTE]
> ***Your Answer***
>
> *(Yes. The endpoint attached to the LOAN entity coming from BOOK is ──O<──, indicating that a book can participate in zero or many loans.)*
>
>
>
>

c) What type of entity is Loan in this diagram? Is it a junction/associative entity? Why?


> [!NOTE]
> ***Your Answer***
>
> *(Loan is a junction (associative) entity. It resolves the many-to-many (M:N) relationship between BOOK and MEMBER, tracking which member borrowed which book over time.)*
>
>
>
>

d) What is the cardinality of the Author-Book relationship? Is this realistic? What might be a more accurate model?


> [!NOTE]
> ***Your Answer***
>
> *(The diagram shows a 1:N relationship, meaning a book can only have exactly one author. This is not realistic for the real world, as books often have multiple co-authors. A more accurate model would use a M:N relationship resolved by a BookAuthor junction table.)*
>
>
>
>

e) What attributes would you add to the Loan entity?


> [!NOTE]
> ***Your Answer***
>
> *(Attributes should include loan_date, due_date, return_date, and status (e.g., active, returned, overdue).)*
>
>
>
>

### Diagram B: School System

```
STUDENT ──O|──────O<── ENROLLMENT ──>|──||── COURSE
                                        │
                                    ||  │
                                        │
                                    O<  │
                                        │
                                   TEACHER
```

Relationships:
- Student `──O|──────O<──` Enrollment (a student may have zero or many enrollments)
- Enrollment `──||──────||──` Course (each enrollment is for exactly one course)
- Teacher `──||──────O<──` Course (each course has zero or many sections, each taught by exactly one teacher)

**Questions:**

a) Can a student exist without being enrolled in any course?


> [!NOTE]
> ***Your Answer***
>
> *(Yes. The relationship from Student to Enrollment is optional on the Enrollment side (zero or many).)*
>
>
>
>

b) Can a course exist without having any enrolled students?


> [!NOTE]
> ***Your Answer***
>
> *(Yes. Given standard M:N resolution logic for Enrollments, a course entity exists independently and can have zero enrollments before students sign up.)*
>
>
>
>

c) What is the cardinality between Student and Course (through Enrollment)?


> [!NOTE]
> ***Your Answer***
>
> *(Many-to-many (M:N). A student takes many courses, and a course contains many students.)*
>
>
>
>

d) Can a teacher exist without teaching any courses?


> [!NOTE]
> ***Your Answer***
>
> *(Yes. The relationship shows TEACHER ──||────O<── COURSE, meaning the minimum participation on the Course side is zero (optional).)*
>
>
>
>

e) Is the Teacher-Course relationship 1:1 or 1:N? What does this imply about team teaching?


> [!NOTE]
> ***Your Answer***
>
> *(It is a 1:N relationship (one teacher, many courses). Because a course maps to exactly one teacher (──||──), this implies team teaching (multiple teachers instructing a single course) is not permitted by this data model.)*
>
>
>
>

---

## Exercise 4: ER Diagram Creation — Gym/Fitness Center

### Scenario

FitZone is a local gym and fitness center. They need a database to manage their operations. Here are the business rules:

1. The gym has **members**. Each member has an ID, first name, last name, email, phone, date of birth, and membership start date.

2. The gym offers **membership plans** (e.g., "Basic", "Premium", "Student"). Each plan has a plan ID, name, monthly price, and description. Each member subscribes to exactly one plan. A plan can have many members.

3. The gym has **trainers** (employees who lead classes). Each trainer has an ID, first name, last name, specialization (e.g., "Yoga", "CrossFit"), and hire date.

4. The gym offers **classes** (e.g., "Morning Yoga", "HIIT Blast"). Each class has an ID, name, day of the week, start time, end time, and maximum capacity. Each class is led by exactly one trainer, but a trainer can lead many classes.

5. Members can **register** for classes. A member can register for many classes, and a class can have many registered members. The registration records the registration date.

6. The gym has **equipment** (treadmills, dumbbells, etc.). Each piece of equipment has an ID, name, type, purchase date, and status ("working", "maintenance", "retired").

7. When equipment breaks, a **maintenance request** is created. Each request has an ID, request date, description of the problem, status ("open", "in progress", "closed"), and resolution date. Each request is for exactly one piece of equipment. One piece of equipment can have many maintenance requests over time.

### Task

1. Identify all entities and their attributes (including key attributes).

> [!NOTE]
> ***Your Answer***
>
> *(
* Member: member_id (PK), first_name, last_name, email, phone, date_of_birth, membership_start_date
* Plan: plan_id (PK), name, monthly_price, description
* Trainer: trainer_id (PK), first_name, last_name, specialization, hire_date
* Class: class_id (PK), name, day_of_week, start_time, end_time, max_capacity
* Equipment: equipment_id (PK), name, type, purchase_date, status
* MaintenanceRequest: request_id (PK), request_date, description, status, resolution_date
* Registration (Junction): member_id (PK, FK), class_id (PK, FK), registration_date)*
>
>
>
>

2. Identify all relationships with their cardinality and participation constraints.

> [!NOTE]
> ***Your Answer***
>
> *(

* Plan ↔ Member: 1:N. Mandatory for Member (exactly one Plan), optional for Plan (zero or many Members).
* Trainer ↔ Class: 1:N. Mandatory for Class (exactly one Trainer), optional for Trainer (zero or many Classes).
* Member ↔ Class: M:N (resolved via Registration).
* Member ↔ Registration: 1:N. Mandatory for Registration, optional for Member.
* Class ↔ Registration: 1:N. Mandatory for Registration, optional for Class.
* Equipment ↔ MaintenanceRequest: 1:N. Mandatory for MaintenanceRequest (exactly one Equipment), optional for Equipment (zero or many Requests).

)*
>
>
>
>

3. Draw a complete ER diagram using crow's foot notation.

> [!NOTE]
> ***Your Answer***
>
> *(

https://imgur.com/a/CyBdxDS

)*
>
>
>
>

4. Identify any entity that might be considered a weak entity or a junction/associative entity. Justify your answer.

> [!NOTE]
> ***Your Answer***
>
> *(Registration is both a junction (associative) entity and a weak entity. It is a junction because it resolves the M:N relationship between Member and Class. It is weak because it cannot be uniquely identified without the member_id and class_id of the parent entities; it has no independent primary key of its own.)*
>
>
>
>

5. Are there any M:N relationships? If so, what junction entity resolves them?

> [!NOTE]
> ***Your Answer***
>
> *(Yes. The relationship between Member and Class is Many-to-Many (M:N), as members can attend many classes and classes have many members. This is resolved by the Registration junction entity.)*
>
>
>
>
---

## Exercise 5: Find and Correct the Errors

The following ER diagram description contains **four errors**. Find each error, explain why it's wrong, and provide the correction.

### Scenario: Online Bookstore

**Entities and attributes:**

1. **Books**
   - book_id (PK)
   - title
   - author_name
   - price
   - genres (stores "Fiction, Mystery, Thriller" as a comma-separated string)

2. **Customer**
   - customer_id (PK)
   - full_name
   - address

3. **Purchase**
   - purchase_id (PK)
   - purchase_date
   - total_amount

**Relationships:**
- Books to Customer: M:N (implemented directly — no junction table)
- Customer to Purchase: 1:N (one customer, many purchases)
- Books to Purchase: no relationship defined

### Your Task

Find the four errors in this design and for each one:

a) State what the error is
> [!NOTE]
> ***Your Answer***
>
> *(

* Error 1: The genres attribute in the Books entity stores a comma-separated string.
* Error 2: The naming convention is inconsistent; Books is plural while the others are singular.
* Error 3: The Books to Customer relationship is modeled as a direct M:N relationship without a junction table.
* Error 4: There is no relationship defined between Books and Purchase.

)*
>
>
>
>

b) Explain why it's a problem (reference the relevant theory section)

> [!NOTE]
> ***Your Answer***
>
> *(

* Error 1: Storing multivalued attributes in one column violates atomicity (1NF) as discussed in Section 6.4, making it impossible to efficiently query or filter by a single genre.
* Error 2: Best practices (Section 11.1) dictate that entity names should be singular nouns (e.g., Book), as the entity represents a single instance template, not a collection.
* Error 3: Relational databases cannot directly implement M:N relationships (Section 10). Storing multiple IDs in columns causes data redundancy and integrity failures.
* Error 4: A purchase is meaningless if we don't know what items were bought. Without a relationship between Books and Purchases, the database cannot record the contents of an order.

)*
>
>
>
>

c) Describe how to fix it

> [!NOTE]
> ***Your Answer***
>
> *(
* Error 1: Remove genres from the Book entity and create a separate BookGenre table containing book_id and genre_name.
* Error 2: Rename the Books entity to Book.
* Error 3: Remove the direct M:N line between Book and Customer. Customers interact with books through their purchases.
* Error 4: Create an attributed junction entity named PurchaseItem between Purchase and Book. This will resolve the M:N relationship (Purchases contain many books, Books appear in many purchases) and should contain quantity and price_at_purchase.

)*
>
>
>
>

**Hints:** Think about multivalued attributes, M:N relationships, entity naming conventions, and missing relationships.

---

## Submission Checklist

- [x ] Exercise 1: ER diagram + design decision paragraph (including why Week 37's category FK is replaced)
- [x ] Exercise 2: All 12 theory review answers, plus 11b
- [x ] Exercise 3: All questions answered for both Diagram A and Diagram B
- [x ] Exercise 4: Entity list, relationship list, ER diagram, and justifications
- [x ] Exercise 5: Four errors identified with explanations and corrections
