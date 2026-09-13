# Conditionals

## Overview

```python
# Basic
if condition:
    ...

# Two possibilities
if condition:
    ...
else:
    ...

# Multiple possibilities
if condition1:
    ...
elif condition2:
    ...
else:
    ...

# AND
if x > 0 and x < 10:
    ...

# OR
if x == 1 or x == 2:
    ...

# NOT
if not is_valid:
    ...

# IN
if name in names:
    ...

# None
if value is None:
    ...

# One-line conditional
result = "yes" if condition else "no"
```

---

- Comaprison notes:

```text
x == y       # Do their values match?
x is y       # Are they the exact same object?
x is None    # Correct way to check for None
```

---

## Example

```python
# Conditionals in Python

# if statement:
# The if statement is used to execute a block of code only if the condition is True.
# It may be followed by one or more elif blocks and a final else block.

# Example:
x = 10
if x > 5:
   # print("x is greater than 5")
elif x == 5:
   # print("x is equal to 5")
else:
   # print("x is less than 5")

# Output: x is greater than 5

# Comparison Operators:
# Python supports various comparison operators to compare values.

# Example:
x = 10
if x > 5:
   # print("x is greater than 5") # Output: x is greater than 5

# Logical Operators:
# Python supports logical operators like and, or, and not to combine conditional statements.

# Example:
x = 10
if x > 5 and x < 15:
   # print("x is between 5 and 15") # Output: x is between 5 and 15

# Nested if statements:
# You can also use if statements inside other if statements (nested if statements).

# Example:
x = 10
if x > 5:
    # print("x is greater than 5")
    if x < 15:
       # print("x is less than 15")

# Output:
# x is greater than 5
# x is less than 15

# Ternary Operator:
# Python supports a ternary operator which provides a concise way to write conditional expressions.
# Syntax: value_if_true if condition else value_if_false

# Example:
x = 10
message = "x is greater than 5" if x > 5 else "x is less than or equal to 5"
# print(message) # Output: x is greater than 5
```

---

## NOT operator

```python
is_raining = False

if not is_raining:
    print("Go outside")
```

---

## AND operator

```python
age = 25
has_ticket = True

if age >= 18 and has_ticket:
    print("You can enter")
```

---

## OR operator

```python
day = "Saturday"

if day == "Saturday" or day == "Sunday":
    print("Weekend")
```

---

## IN operator

```python
day = "Saturday"

if day in ["Saturday", "Sunday"]:
    print("Weekend")
```

---

## String

```python
name = "Alice"

if "A" in name:
    print("Name contains A")
```

---

## Dictionaries

```python
person = {"name": "Alice", "age": 25}

if "name" in person:
    print("Name exists")
```

---

## Nested conditionals

```python
age = 25
has_id = True

if age >= 18:
    if has_id:
        print("Entry allowed")
    else:
        print("You need ID")
else:
    print("You are too young")


# don't overuse nesting, often it can be simplified:
if age >= 18 and has_id:
    print("Entry allowed")
else:
    print("Entry denied")
```