# Entity Relationships

## Relational Database Concepts

A typical relational database consists of:

```text
Database
│
├── Tables
│   ├── users
│   ├── products
│   └── orders
│
├── Relationships
│   ├── Primary keys
│   └── Foreign keys
│
├── Indexes
│
├── Constraints
│
└── Transactions
```

Relationships allow tables to work together rather than storing everything in one large table.

- For example:
```text
users
  │
  │ user_id
  ▼
orders
  │
  │ product_id
  ▼
products
```
---

## SQL Relationships

SQL relationships describe how rows in one table relate to rows in another table.

The three fundamental relationship types are:

* **One-to-One (1:1)**
* **One-to-Many (1:N)**
* **Many-to-Many (M:N)**

---

## 1. One-to-One Relationship

A **one-to-one** relationship means that one row in a table is related to **at most one row** in another table.

A user has one profile, and each profile belongs to one user.

```mermaid
erDiagram
    USER ||--|| PROFILE : has

    USER {
        int id PK
        string username
        string email
    }

    PROFILE {
        int id PK
        int user_id FK
        string first_name
        string last_name
    }
```

```text
USER
-----
id
username
email

PROFILE
-------
id
user_id
first_name
last_name
```

The `PROFILE.user_id` column references `USER.id`.

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username VARCHAR(100),
    email VARCHAR(255)
);

CREATE TABLE profiles (
    id INTEGER PRIMARY KEY,
    user_id INTEGER UNIQUE,
    first_name VARCHAR(100),
    last_name VARCHAR(100),

    FOREIGN KEY (user_id)
        REFERENCES users(id)
);
```

The `UNIQUE` constraint on `user_id` prevents multiple profiles from belonging to the same user.

---

## 2. One-to-Many Relationship

A **one-to-many** relationship means that one row in a table can be related to **many rows** in another table.

This is one of the most common relationships in relational databases.

One customer can have many orders.

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places

    CUSTOMER {
        int id PK
        string name
        string email
    }

    ORDER {
        int id PK
        int customer_id FK
        decimal total
        date order_date
    }
```

The relationship can be understood as:

```text
CUSTOMER
   │
   ├── ORDER
   ├── ORDER
   ├── ORDER
   └── ORDER
```

The foreign key is stored on the **many side**:

```text
CUSTOMER.id
     ▲
     │
ORDER.customer_id
```

- SQL

```sql
CREATE TABLE customers (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255)
);

CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    total DECIMAL(10, 2),
    order_date DATE,

    FOREIGN KEY (customer_id)
        REFERENCES customers(id)
);
```

- Querying the relationship

```sql
SELECT
    customers.name,
    orders.id,
    orders.total
FROM customers
JOIN orders
    ON customers.id = orders.customer_id;
```

---

## 3. Many-to-Many Relationship

A **many-to-many** relationship means that:

* One row in table A can relate to many rows in table B.
* One row in table B can relate to many rows in table A.

Relational databases normally implement this using a **junction table** (also called an associative or bridge table).

Students can enrol in many courses, and each course can have many students.

```mermaid
erDiagram
    STUDENT ||--o{ ENROLLMENT : has
    COURSE ||--o{ ENROLLMENT : contains

    STUDENT {
        int id PK
        string name
        string email
    }

    COURSE {
        int id PK
        string name
        int credits
    }

    ENROLLMENT {
        int student_id PK, FK
        int course_id PK, FK
        date enrolled_at
    }
```

The relationship becomes:

```text
STUDENT
   │
   ├── ENROLLMENT ─── COURSE
   ├── ENROLLMENT ─── COURSE
   └── ENROLLMENT ─── COURSE
```

The `ENROLLMENT` table breaks the many-to-many relationship into two one-to-many relationships.

```text
STUDENT  1 ─── N  ENROLLMENT  N ─── 1  COURSE
```

- SQL

```sql
CREATE TABLE students (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255)
);

CREATE TABLE courses (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    credits INTEGER
);

CREATE TABLE enrollments (
    student_id INTEGER,
    course_id INTEGER,
    enrolled_at DATE,

    PRIMARY KEY (student_id, course_id),

    FOREIGN KEY (student_id)
        REFERENCES students(id),

    FOREIGN KEY (course_id)
        REFERENCES courses(id)
);
```

The composite primary key prevents the same student from being enrolled in the same course more than once.

---

## 4. Foreign Key Relationship

A **foreign key** connects a column in one table to a primary key or unique key in another table.

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : has

    CUSTOMER {
        int id PK
        string name
    }

    ORDER {
        int id PK
        int customer_id FK
    }
```

The relationship is:

```text
CUSTOMER
    id  ←──────── customer_id
                     │
                   ORDER
```

- SQL

```sql
FOREIGN KEY (customer_id)
    REFERENCES customers(id)
```

The foreign key maintains **referential integrity**.

---

## 5. Self-Referencing Relationship

A table can also reference **itself**.

This is useful for hierarchical data such as employees and managers.

```mermaid
erDiagram
    EMPLOYEE ||--o{ EMPLOYEE : manages

    EMPLOYEE {
        int id PK
        int manager_id FK
        string name
        string job_title
    }
```

Example:

```text
CEO
├── Engineering Manager
│   ├── Developer
│   └── Developer
└── Sales Manager
    ├── Salesperson
    └── Salesperson
```

The table contains a foreign key pointing back to itself:

```sql
CREATE TABLE employees (
    id INTEGER PRIMARY KEY,
    manager_id INTEGER,
    name VARCHAR(100),
    job_title VARCHAR(100),

    FOREIGN KEY (manager_id)
        REFERENCES employees(id)
);
```

The CEO can have a `NULL` `manager_id` because they do not report to another employee.

---

## 6. Multiple Relationships

A table can participate in several relationships.

For example:

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : included_in

    CUSTOMER {
        int id PK
        string name
        string email
    }

    ORDER {
        int id PK
        int customer_id FK
        date order_date
        decimal total
    }

    ORDER_ITEM {
        int id PK
        int order_id FK
        int product_id FK
        int quantity
        decimal unit_price
    }

    PRODUCT {
        int id PK
        string name
        decimal price
    }
```

This represents a typical e-commerce database:

```text
CUSTOMER
   │
   │ 1:N
   ▼
 ORDER
   │
   │ 1:N
   ▼
ORDER_ITEM
   ▲
   │ N:1
   │
PRODUCT
```

A customer can have many orders.

An order can contain many order items.

A product can appear in many order items.

---

## 7. Relationship Cardinality

Mermaid ER diagrams use symbols to represent **cardinality**.

| Mermaid | Meaning      |
| ------- | ------------ |
| `\|\|`  | Exactly one  |
| `o\|`   | Zero or one  |
| `o{`    | Zero or many |
| `\|{`   | One or many  |

- Common combinations

```mermaid
erDiagram
    A ||--|| B : "1:1"
    C ||--o{ D : "1:N"
    E o|--o{ F : "0..1 to many"
    G ||--|{ H : "1:N"
```

The symbols can be read from left to right.

For example:

```text
CUSTOMER ||--o{ ORDER
```

means:

```text
One CUSTOMER
     │
     └──── zero or many ORDERS
```

---

## 8. Optional vs Required Relationships

Cardinality also tells us whether a relationship is optional.

### Optional

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
```

A customer can exist without having any orders.

```text
CUSTOMER
   │
   ├── ORDER
   ├── ORDER
   └── no orders is also valid
```

### Required

```mermaid
erDiagram
    ORDER ||--|{ ORDER_ITEM : contains
```

An order must contain **one or more** order items.

```text
ORDER
   │
   ├── ORDER_ITEM
   ├── ORDER_ITEM
   └── ...
```

---

## 9. Relationships and JOINs

SQL relationships are commonly used with `JOIN`.

Consider:

```mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places

    CUSTOMER {
        int id PK
        string name
    }

    ORDER {
        int id PK
        int customer_id FK
        decimal total
    }
```

The foreign key relationship is:

```text
customers.id
     ▲
     │
orders.customer_id
```

The corresponding SQL `JOIN` is:

```sql
SELECT
    customers.name,
    orders.total
FROM customers
JOIN orders
    ON customers.id = orders.customer_id;
```

The `ON` condition tells SQL **how the tables are related**.

---

## 10. Relationship Summary

```mermaid
flowchart TD
    A[SQL Relationships] --> B[One-to-One]
    A --> C[One-to-Many]
    A --> D[Many-to-Many]
    A --> E[Self-Referencing]

    B --> B1[User → Profile]

    C --> C1[Customer → Orders]

    D --> D1[Students ↔ Courses]
    D1 --> D2[Junction Table]

    E --> E1[Employee → Manager]
```

| Relationship       | Example            | Typical Implementation             |
| ------------------ | ------------------ | ---------------------------------- |
| **1:1**            | User → Profile     | Foreign key + `UNIQUE`             |
| **1:N**            | Customer → Orders  | Foreign key on the many side       |
| **N:M**            | Students ↔ Courses | Junction table                     |
| **Self-reference** | Employee → Manager | Foreign key referencing same table |

---

## Key Rule

For relational database design, remember:

```text
1:1  →  Foreign key + UNIQUE
1:N  →  Foreign key on the "many" side
N:M  →  Junction / bridge table
```

The foreign key is the mechanism that connects related rows, while **cardinality** describes how many rows can participate in that relationship.