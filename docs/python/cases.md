# Match/Case

## Overview

- Sytnax:

```python
match value:
    case pattern1:
        # code
    case pattern2:
        # code
    case _:
        # default
```

- Example:

```python
day = "Monday"

match day:
    case "Monday":
        print("Start of the week")
    case "Friday":
        print("Almost the weekend")
    case "Saturday" | "Sunday":
        print("Weekend")
    case _:
        print("Another weekday")
```

!!! tip "The `_` means:"

    "Anything else."

    Think of it like the else in an if statement.

---

## match vs if/elif

| Situation                  | Prefer  |
| -------------------------- | ------- |
| Exact value matching       | `match` |
| Many possible exact values | `match` |
| Commands/options           | `match` |
| Matching data structures   | `match` |
| Matching lists/dicts       | `match` |
| Complex conditions         | `if`    |
| Ranges                     | `if`    |
| Boolean logic              | `if`    |
| Simple true/false decision | `if`    |


- conditional:

```python
if day == "Monday":
    print("Start")
elif day == "Friday":
    print("Almost weekend")
elif day == "Saturday" or day == "Sunday":
    print("Weekend")
else:
    print("Weekday")
```

- match/case:

```python
match day:
    case "Monday":
        print("Start")
    case "Friday":
        print("Almost weekend")
    case "Saturday" | "Sunday":
        print("Weekend")
    case _:
        print("Weekday")
```

## Matching multiple values with `|`

- The pipe `|` means OR inside a pattern.

```python
command = "quit"

match command:
    case "quit" | "exit" | "q":
        print("Exiting...")
    case "help" | "h":
        print("Showing help")
    case _:
        print("Unknown command")
```

---

## The wildcard `_`

```python
status = 500

match status:
    case 200:
        print("Success")
    case 404:
        print("Not found")
    case _:
        print("Something else")
```

!!! danger "Important"

    Put `_` last:

    match value:
        case _:
            print("Anything")
        case 1:
            print("One")

    The case 1 will never be reached.

---

## Matching numbers

```python
choice = 2

match choice:
    case 1:
        print("Start")
    case 2:
        print("Settings")
    case 3:
        print("Exit")
    case _:
        print("Invalid choice")
```

---

## Matching with conditions — if guards

```python
age = 25

match age:
    case age if age < 18:
        print("Minor")
    case age if age >= 18:
        print("Adult")
```

> The if after the pattern is called a guard.

```python
number = 10

match number:
    case n if n > 0:
        print("Positive")
    case n if n < 0:
        print("Negative")
    case 0:
        print("Zero")
```

---

## Matching strings

```python
command = input("Command: ")

match command:
    case "start":
        print("Starting...")
    case "stop":
        print("Stopping...")
    case "pause":
        print("Pausing...")
    case _:
        print("Unknown command")
```

---

## Matching lists

```python
numbers = [1, 2, 3]

match numbers:
    case []:
        print("Empty")
    case [x]:
        print("One item:", x)
    case [x, y]:
        print("Two items:", x, y)
    case [x, y, z]:
        print("Three items:", x, y, z)
```

---

## Matching dictionaries

```python
user = {
    "name": "Alice",
    "role": "admin"
}

match user:
    case {"role": "admin"}:
        print("Administrator")
    case {"role": "user"}:
        print("Regular user")
    case _:
        print("Unknown role")
```

## Matching different data types

```python
value = 10

match value:
    case int():
        print("Integer")
    case str():
        print("String")
    case float():
        print("Float")
    case _:
```

---

## Matching objects/classes

Match can also work with classes.

For example:

```python
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Then:

```python
user = User("Alice", 25)

match user:
    case User(name, age):
        print(f"{name} is {age}")
```

---

## Using asterix `*` in patterns

- First example:

```python
numbers = [1, 2, 3, 4, 5]

match numbers:
    case [first, *rest]:
        print("First:", first)
        print("Rest:", rest)

# Output:
# First: 1
# Rest: [2, 3, 4, 5]
```

- Second example:

```python
numbers = [1, 2, 3, 4, 5]

match numbers:
    case [first, *middle, last]:
        print(first)
        print(middle)
        print(last)

# result
# 1
# [2, 3, 4]
# 5
```