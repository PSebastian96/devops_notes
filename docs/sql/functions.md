## 1. Aggregate Functions

  Function    Purpose                      Example
  ----------- ---------------------------- --------------
  `COUNT()`   Counts rows/values           `COUNT(*)`
  `SUM()`     Adds values                  `SUM(price)`
  `AVG()`     Calculates arithmetic mean   `AVG(price)`
  `MIN()`     Finds minimum                `MIN(price)`
  `MAX()`     Finds maximum                `MAX(price)`

Example:

``` sql
SELECT
    COUNT(*) AS number_of_customers,
    AVG(age) AS average_age,
    MIN(age) AS youngest,
    MAX(age) AS oldest
FROM customers;
```

------------------------------------------------------------------------

## 2. `COUNT()` --- Important Distinctions

  Expression                 Meaning
  -------------------------- -----------------------------------------
  `COUNT(*)`                 Counts rows
  `COUNT(column)`            Counts non-`NULL` values in that column
  `COUNT(DISTINCT column)`   Counts unique non-`NULL` values

If 100 customers exist but 5 have `NULL` emails:

``` text
COUNT(*)      = 100
COUNT(email)  = 95
```

------------------------------------------------------------------------

## 3. String Functions

  Function/operator   Purpose                          Example
  ------------------- -------------------------------- --------------------------------------
  `LIKE`              Pattern matching                 `name LIKE 'Leo%'`
  `NOT LIKE`          Negated pattern matching         `name NOT LIKE 'Leo%'`
  `LOWER()`           Converts to lowercase            `LOWER(name)`
  `UPPER()`           Converts to uppercase            `UPPER(name)`
  `LENGTH()`          Gets string length               `LENGTH(name)`
  `TRIM()`            Removes surrounding whitespace   `TRIM(name)`
  `SUBSTRING()`       Extracts part of a string        `SUBSTRING(name, 1, 3)`
  `REPLACE()`         Replaces text                    `REPLACE(name, 'old', 'new')`
  `CONCAT()`          Joins strings                    `CONCAT(first_name, ' ', last_name)`
  `LEFT()`            Takes characters from left       `LEFT(name, 3)`
  `RIGHT()`           Takes characters from right      `RIGHT(name, 3)`

> **Note:** Exact availability and syntax vary between PostgreSQL,
> MySQL, SQL Server, SQLite, Oracle, etc.

------------------------------------------------------------------------

## 4. `LIKE` --- Pattern Matching

### Wildcards

  Wildcard   Meaning                   Example
  ---------- ------------------------- ----------
  `%`        Zero or more characters   `'Leo%'`
  `_`        Exactly one character     `'Le_n'`

### Examples

``` sql
WHERE name LIKE 'Leo%'
```

Matches:

``` text
Leo
Leon
Leonard
Leona
```

because `%` means anything after `Leo`.

### Ends with `leon`

``` sql
WHERE name LIKE '%leon'
```

Matches values ending in `leon`.

### Exactly one character before `leon`

``` sql
WHERE name LIKE '_leon'
```

Means exactly one character followed by `leon`.

### At least one character before `leon`

``` sql
WHERE name LIKE '_%leon'
```

Means:

-   `_` = exactly one character
-   `%` = zero or more characters
-   `leon` = must occur at the end

Examples:

``` text
Aleon       ✓
Napoleon    ✓
XXleon      ✓
leon        ✗
```

------------------------------------------------------------------------

## 5. Numeric Functions

  Function                 Purpose                    Example
  ------------------------ -------------------------- ----------------------
  `ROUND()`                Rounds a number            `ROUND(price, 2)`
  `CEIL()` / `CEILING()`   Rounds upward              `CEILING(4.2)` → `5`
  `FLOOR()`                Rounds downward            `FLOOR(4.8)` → `4`
  `ABS()`                  Absolute value             `ABS(-10)` → `10`
  `MOD()`                  Remainder                  `MOD(10, 3)` → `1`
  `POWER()`                Raises number to a power   `POWER(2, 3)` → `8`
  `SQRT()`                 Square root                `SQRT(16)` → `4`

------------------------------------------------------------------------

## 6. Date/Time Functions

These vary more significantly between SQL implementations.

  Function/concept      Purpose                      Example
  --------------------- ---------------------------- ---------------------------
  `CURRENT_DATE`        Today's date                 `CURRENT_DATE`
  `CURRENT_TIMESTAMP`   Current date + time          `CURRENT_TIMESTAMP`
  `EXTRACT`             Gets a date/time component   `EXTRACT(YEAR FROM date)`

Example:

``` sql
SELECT EXTRACT(YEAR FROM order_date)
FROM orders;
```

Date arithmetic is database-specific.

------------------------------------------------------------------------

## 7. `NULL` Functions

  -------------------------------------------------------------------------------
  Function                Purpose                 Example
  ----------------------- ----------------------- -------------------------------
  `COALESCE()`            Returns the first       `COALESCE(phone, 'No phone')`
                          non-`NULL` value        

  `NULLIF()`              Returns `NULL` if two   `NULLIF(score, 0)`
                          values are equal        
  -------------------------------------------------------------------------------

Example:

``` sql
SELECT COALESCE(phone, 'No phone number')
FROM customers;
```

If `phone` is `NULL`, the result is:

``` text
No phone number
```

------------------------------------------------------------------------

## 8. `CASE` Expressions

`CASE` is SQL's equivalent of conditional `if / else` logic.

``` sql
SELECT
    name,
    CASE
        WHEN age >= 18 THEN 'Adult'
        ELSE 'Child'
    END AS age_group
FROM customers;
```

Multiple conditions:

``` sql
CASE
    WHEN score >= 70 THEN 'Distinction'
    WHEN score >= 40 THEN 'Pass'
    ELSE 'Fail'
END
```

------------------------------------------------------------------------

## 9. Comparison Operators

  Operator   Meaning              Example
  ---------- -------------------- -------------
  `=`        Equal                `age = 18`
  `<>`       Not equal            `age <> 18`
  `!=`       Not equal            `age != 18`
  `>`        Greater than         `age > 18`
  `<`        Less than            `age < 18`
  `>=`       Greater than/equal   `age >= 18`
  `<=`       Less than/equal      `age <= 18`

------------------------------------------------------------------------

## 10. Boolean Logic Precedence

A useful simplified precedence is:

    Precedence Operator
  ------------ --------------------------------------------------
             1 Parentheses `()`
             2 Comparison operators (`=`, `>`, `<`, `>=`, etc.)
             3 `NOT`
             4 `AND`
             5 `OR`

For example:

``` sql
WHERE A OR B AND C
```

is generally interpreted as:

``` sql
WHERE A OR (B AND C)
```

Safer and clearer:

``` sql
WHERE age > 18
  AND (country = 'UK' OR country = 'US')
```

------------------------------------------------------------------------