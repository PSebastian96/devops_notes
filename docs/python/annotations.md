# Python Annotations

## Overview

* Annotations don't enforce types at runtime.

* Type annotations are primarily for readability, IDEs, and static type checkers such as mypy or pyright.

```python
def add(a: int, b: int) -> int:
    return a + b


# Python itself doesn't stop you from doing:

add("hello", "world")
```

- Mental model for writing typed Python:

```text
                 Python value
                      │
          ┌───────────┴───────────┐
          │                       │
       known type             unknown type
          │                       │
          ▼                       ▼
   annotate precisely       ┌─────┴─────┐
                            │           │
                           Any       object
                            │           │
                     "skip checking"  "check/narrow"
```

---

- writing typed Python for APIs:

```text
Function
   │
   ├── parameters → annotate
   │
   ├── return value → annotate
   │
   └── local variables → usually infer
```

---

- And for collections:

```text
Need to iterate?      → Iterable[T]
Need indexing/order?  → Sequence[T]
Need a mutable list?  → list[T]
Need mapping?          → Mapping[K, V]
Need a concrete dict?  → dict[K, V]
```

---

## Reference

```python
# Variables
name: str = "Alice"
age: int = 25

# Lists
names: list[str] = ["Alice", "Bob"]

# Dictionaries
users: dict[str, int] = {"Alice": 25}

# Optional
name: str | None = None

# Function parameters + return
def add(a: int, b: int) -> int:
    return a + b

# No return value
def print_name(name: str) -> None:
    print(name)

# Class attributes
class User:
    name: str
    age: int

# Class methods
class User:
    def greet(self, message: str) -> str:
        return message
```

---

- Syntax:

```bash
variable_name: type = value
```

- Example:

```python
name: str = "Alice"
age: int = 25
price: float = 19.99
is_active: bool = True
```

- Annotate without assigning:

```python
name: str
age: int
```

---

### Variables

```python
name: str = "Alice"
age: int = 25
price: float = 10.5
active: bool = True
```

---

### List

```python
names: list[str] = ["Alice", "Bob"]
numbers: list[int] = [1, 2, 3]
```

---

### Dictionary

```python
ages: dict[str, int] = {
    "Alice": 25,
    "Bob": 30
}
```

---

### Tuple

```python
point: tuple[int, int] = (10, 20)
```

---

### Set

```python
tags: set[str] = {"python", "coding"}
```

---

### None

- If a variable can contain either a string or None:

```python
name: str | None = None
```

```python
name = "Alice"
name = None

""" 
name = "Alice"
name = None
"""
```

---

## Functions

### Basic

```python
def add(a: int, b: int) -> int:
    return a + b

"""
The annotations tells:
a       → int
b       → int
return  → int
"""

# Call it:
result = add(10, 20)
```

### Return Nothing

- This means the function doesn't return a value.

```python
def print_name(name: str) -> None:
    print(name)
```

### Multiple types

- A parameter can accept more than one type:

```python
def print_id(user_id: int | str) -> None:
    print(user_id)

# Both are valid
print_id(123)
print_id("ABC123")
```

### String function

```python
def greet(name: str) -> str:
    return f"Hello, {name}"

# Call:
message = greet("Alice")
```

### Lists in functions

```python
# Example 1:
def get_total(numbers: list[int]) -> int:
    return sum(numbers)

numbers = [1, 2, 3, 4]

total = get_total(numbers)

# Example 2:
def greet_all(names: list[str]) -> None:
    for name in names:
        print(f"Hello {name}")
```

---

### `*args` and `**kwargs`

```python
# Variable positional arguments:

def total(*numbers: int) -> int:
    return sum(numbers)

# Variable keyword arguments:

def create_user(**attributes: str) -> dict[str, str]:
    return attributes

# Calling:

total(1, 2, 3, 4)

create_user(name="Alice", city="London")
```

---

## Classes

### Basic 

- Class attribute annotation:

```python
class User:
    name: str
    age: int

    def __init__(self, name: str, age: int) -> None:
        self.name = name
        self.age = age

# Usage:
user = User("Alice", 25)

print(user.name)
print(user.age)
```

### Annotating `self`

```python
class User:
    def __init__(self, name: str, age: int) -> None:
        self.name = name
        self.age = age
```

### Methods

```python
class Calculator:
    def add(self, a: int, b: int) -> int:
        return a + b

# Usage:
calculator = Calculator()
result = calculator.add(10, 20)
```

### Returning a class instance

```python
class User:
    def __init__(self, name: str) -> None:
        self.name = name


def create_user() -> User:
    return User("Alice")

# Then, the type of user is known to be User.
user = create_user()
```

---

## Example

```python
class Product:
    name: str
    price: float

    def __init__(self, name: str, price: float) -> None:
        self.name = name
        self.price = price

    def get_price(self) -> float:
        return self.price


def print_product(product: Product) -> None:
    print(product.name)
    print(product.price)


product = Product("Laptop", 999.99)

print_product(product)
```

- The annotations make the structure very clear:

```text
Product
├── name  → str
├── price → float
│
├── __init__
│   ├── name  → str
│   ├── price → float
│   └── returns → None
│
└── get_price
    └── returns → float

print_product
└── product → Product
    returns → None
```

---

## Any vs object

### Any

- Any: type of returned data does not matter, not important.

```python
from typing import Any

value: Any
"""
Any essentially tells the type checker:

'Don't type-check this value.'
"""

# can do almost anything with it:
value: Any = 42

value.foo()
value.bar
value + "hello"
value["something"]

# A static type checker generally won't complain.
```

---

### Object

- Objecy: 'this can be literally any Python object'.

- Unlike Any, Python's type checker does not allow arbitrary operations.

```python
value: object = 42

value + 1       # Type checker: error
value.foo()     # Type checker: error

value: object = 42

if isinstance(value, int):
    print(value + 1)
```

---

### The key distinction

|                                 | `Any`         | `object`  |
| ------------------------------- | ------------- | --------- |
| Represents any value            | ✅             | ✅         |
| Type checking                   | ❌ bypasses it | ✅         |
| Can call arbitrary methods      | ✅             | ❌         |
| Can access arbitrary attributes | ✅             | ❌         |
| Safe default for unknown values | ❌             | ✅         |
| Useful for dynamic APIs         | ✅             | Sometimes |