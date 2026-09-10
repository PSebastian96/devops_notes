# Syntax

## 1. Core SQL Clauses and Keywords

| SQL Element       | What It Does                                   | Example                                             |
| ----------------- | ---------------------------------------------- | --------------------------------------------------- |
| `SELECT`          | Chooses which columns or expressions to return | `SELECT name, age`                                  |
| `FROM`            | Specifies the table to retrieve data from      | `FROM customers`                                    |
| `WHERE`           | Filters individual rows                        | `WHERE age > 18`                                    |
| `DISTINCT`        | Removes duplicate results                      | `SELECT DISTINCT country`                           |
| `AS`              | Gives a column or table an alias               | `SELECT name AS customer_name`                      |
| `AND`             | Requires multiple conditions to be true        | `WHERE age > 18 AND country = 'UK'`                 |
| `OR`              | Allows either condition to be true             | `WHERE country = 'UK' OR country = 'US'`            |
| `NOT`             | Negates a condition                            | `WHERE NOT country = 'UK'`                          |
| `IN`              | Checks whether a value is in a list            | `WHERE country IN ('UK', 'US', 'FR')`               |
| `NOT IN`          | Checks that a value isn't in a list            | `WHERE country NOT IN ('UK', 'US')`                 |
| `BETWEEN`         | Checks whether a value falls within a range    | `WHERE age BETWEEN 18 AND 30`                       |
| `LIKE`            | Performs pattern matching on strings           | `WHERE name LIKE 'A%'`                              |
| `NOT LIKE`        | Performs negated pattern matching              | `WHERE name NOT LIKE 'A%'`                          |
| `IS NULL`         | Checks whether a value is `NULL`               | `WHERE email IS NULL`                               |
| `IS NOT NULL`     | Checks that a value isn't `NULL`               | `WHERE email IS NOT NULL`                           |
| `ORDER BY`        | Sorts the result                               | `ORDER BY age DESC`                                 |
| `ASC`             | Sorts in ascending order                       | `ORDER BY age ASC`                                  |
| `DESC`            | Sorts in descending order                      | `ORDER BY age DESC`                                 |
| `LIMIT`           | Restricts the number of rows returned          | `LIMIT 10`                                          |
| `OFFSET`          | Skips a number of rows                         | `LIMIT 10 OFFSET 20`                                |
| `GROUP BY`        | Groups rows for aggregate calculations         | `GROUP BY country`                                  |
| `HAVING`          | Filters groups after `GROUP BY`                | `HAVING COUNT(*) > 5`                               |
| `JOIN`            | Combines rows from multiple tables             | `JOIN orders ON customers.id = orders.customer_id`  |
| `ON`              | Specifies the condition used by a `JOIN`       | `ON a.id = b.id`                                    |
| `LEFT JOIN`       | Keeps all rows from the left table             | `FROM customers LEFT JOIN orders ...`               |
| `INNER JOIN`      | Returns matching rows from both tables         | `FROM customers INNER JOIN orders ...`              |
| `RIGHT JOIN`      | Keeps all rows from the right table            | `FROM a RIGHT JOIN b ...`                           |
| `FULL OUTER JOIN` | Keeps unmatched rows from both tables          | `FROM a FULL OUTER JOIN b ...`                      |
| `UNION`           | Combines results and removes duplicates        | `SELECT ... UNION SELECT ...`                       |
| `UNION ALL`       | Combines results while keeping duplicates      | `SELECT ... UNION ALL SELECT ...`                   |
| `CASE`            | Provides conditional logic                     | `CASE WHEN age >= 18 THEN 'Adult' ELSE 'Child' END` |
| `WHEN`            | Defines a condition inside `CASE`              | `WHEN score >= 50`                                  |
| `THEN`            | Defines the result of a `WHEN` condition       | `THEN 'Pass'`                                       |
| `ELSE`            | Defines the fallback result in `CASE`          | `ELSE 'Fail'`                                       |
| `END`             | Closes a `CASE` expression                     | `END AS result`                                     |


---

## 2. Written SQL Syntax Order

``` sql
SELECT columns
FROM table
WHERE conditions
GROUP BY columns
HAVING group_conditions
ORDER BY columns
LIMIT number;
```

`FROM` cannot normally go before `SELECT` in standard `SELECT`-query
syntax.

Example:

``` sql
SELECT country, COUNT(*) AS customers
FROM customers
WHERE age >= 18
GROUP BY country
HAVING COUNT(*) > 10
ORDER BY customers DESC
LIMIT 5;
```

---

## 3. SQL Logical Processing Order

| Order | Clause              | What conceptually happens                       |
| ----: | ------------------- | ----------------------------------------------- |
|     1 | `FROM`              | SQL identifies the source table(s).             |
|     2 | `JOIN`              | Tables are combined.                            |
|     3 | `ON`                | Join conditions are applied.                    |
|     4 | `WHERE`             | Individual rows are filtered.                   |
|     5 | `GROUP BY`          | Remaining rows are grouped.                     |
|     6 | Aggregate functions | `COUNT`, `SUM`, `AVG`, etc. are calculated.     |
|     7 | `HAVING`            | Groups are filtered.                            |
|     8 | `SELECT`            | Requested columns and expressions are produced. |
|     9 | `DISTINCT`          | Duplicate output rows are removed.              |
|    10 | `ORDER BY`          | Results are sorted.                             |
|    11 | `LIMIT` / `OFFSET`  | The final number of rows is restricted.         |

!!! tip "Distinguish between `written` and `logical` order"

    SQL is written top-to-bottom, but its logical query processing order is different.

    **Written order:**

    `SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT`

    **Logical order:**

    `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`

---