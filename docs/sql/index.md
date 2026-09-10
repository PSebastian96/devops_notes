# SQL & Databases

## Overview

SQL (Structured Query Language) is used to **store, retrieve, manipulate, and manage data** in relational databases.

Databases are a core component of modern applications and DevOps systems. They store application data such as users, configurations, transactions, logs, and business information, while providing mechanisms for querying, securing, backing up, and maintaining that data.

This section covers the fundamentals of **SQL, relational databases, database design, querying, performance, and administration**, with practical examples using common database systems.

---

## SQL at a Glance

A relational database stores information in **tables**.

For example:

```text
users
┌────┬───────────┬──────────────────────┐
│ id │ name      │ email                │
├────┼───────────┼──────────────────────┤
│  1 │ Alice     │ alice@example.com    │
│  2 │ Bob       │ bob@example.com      │
│  3 │ Charlie   │ charlie@example.com  │
└────┴───────────┴──────────────────────┘
```

SQL allows you to interact with that data:

```sql
SELECT name, email
FROM users
WHERE id = 1;
```

Result:

```text
Alice | alice@example.com
```

---

## SQL vs Database

SQL and a database are related, but they are not the same thing.

| SQL                                    | Database                            |
| -------------------------------------- | ----------------------------------- |
| Query language                         | Data storage system                 |
| Used to interact with data             | Stores and manages data             |
| Defines queries                        | Contains tables and data            |
| `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Users, tables, indexes, constraints |
| Standardized language                  | Implemented by database systems     |

Examples of database systems include:

* PostgreSQL
* MySQL
* MariaDB
* SQLite
* Microsoft SQL Server
* Oracle Database

Although these systems use SQL, each database can have its own extensions, features, and syntax differences.

---

## SQL Command Categories

SQL commands can broadly be grouped into several categories.

### Data Query Language

Used to retrieve data.

```sql
SELECT *
FROM users;
```

### Data Manipulation Language

Used to modify data.

```sql
INSERT INTO users (name, email)
VALUES ('Alice', 'alice@example.com');
```

```sql
UPDATE users
SET email = 'new@example.com'
WHERE id = 1;
```

```sql
DELETE FROM users
WHERE id = 1;
```

### Data Definition Language

Used to define database structures.

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255)
);
```

Other commands include:

```sql
ALTER TABLE
DROP TABLE
CREATE INDEX
```

---

## SQL in Application Development

Applications commonly communicate with a database through a database driver, ORM, or framework.

For example:

```text
Django Application
       │
       ▼
 Django ORM
       │
       ▼
      SQL
       │
       ▼
 PostgreSQL
       │
       ▼
    Database
```

A developer may write Python:

```python
User.objects.filter(is_active=True)
```

while Django generates the corresponding SQL query.

Understanding SQL is therefore useful even when working with an ORM.

---

## SQL in DevOps

Databases are also an important part of infrastructure and deployment.

A typical deployment might look like:

```text
Application
     │
     ▼
Docker Container
     │
     ▼
Database
     │
     ├── PostgreSQL
     ├── MySQL
     └── SQLite
```

DevOps engineers commonly need to understand:

* Database containers
* Persistent volumes
* Environment variables
* Database credentials
* Migrations
* Backups
* Restore procedures
* Database networking
* Connection strings
* Production database configuration
* Monitoring and performance

---

## Development vs Production

A local development environment might use:

```text
Django
  │
  └── SQLite
```

while production might use:

```text
Django
  │
  └── PostgreSQL
```

Or, with Docker:

```text
Docker Compose
│
├── Django
│
└── PostgreSQL
```

The important principle is to understand **where the data lives, how the application connects to it, and how that data is persisted and backed up**.

---

## Quick Reference

| Concept     | Purpose                                             |
| ----------- | --------------------------------------------------- |
| Database    | Stores and manages data                             |
| Table       | Organizes data into rows and columns                |
| Row         | Represents one record                               |
| Column      | Represents an attribute                             |
| Primary Key | Uniquely identifies a record                        |
| Foreign Key | Links records between tables                        |
| JOIN        | Combines data from multiple tables                  |
| Index       | Improves query performance                          |
| Constraint  | Enforces data rules                                 |
| Transaction | Groups database operations                          |
| Migration   | Changes database structure                          |
| ORM         | Maps application objects to database data           |
| SQL         | Language used to interact with relational databases |

> **Core idea:** SQL is the language; PostgreSQL, MySQL, SQLite, and other systems are the database engines that execute it.