# Dunder methods

## Overview

Dunder means double underscore.

A dunder method is a special Python method with names like:

```text
__init__
__str__
__repr__
__len__
__eq__
__add__
```

"Dunder" = double underscore → `__something__`

They let your classes interact with Python's built-in syntax and operations.

```python
# For example, when you write:

len(my_object)

# Python can internally call:

my_object.__len__()

# When you write:

print(my_object)

# Python can call:

my_object.__str__()

"""
So dunder methods are essentially how you tell Python:

When someone uses this built-in operation on my class, here's what should happen.
"""
```

!!! note "What are dunder methods?"
    
    Dunder = double underscore methods, e.g. `__str__`, `__init__`, `__add__`.
    
    They are special methods recognized by Python.
    
    They define or customize how objects behave with Python's built-in syntax and functions.
    
    They act as hooks into Python's object model.


!!! abstract "Are they "default methods"?"

    Not exactly.
    
    They are better understood as special methods that Python knows about.
    
    Some behavior may already be inherited from base classes such as object.
    
    You can define or override these methods in your own class to customize its behavior.
    
    How does Python use them?


When you use normal Python syntax or built-in functions, Python may internally invoke the corresponding dunder method.

### `__str__`:

    `__str__` defines the human-readable string representation of an object.
    
    str(person) asks Python to produce that string representation.
    
    Python uses the object's `__str__` behavior to do this.
    
    You can call person.`__str__()` directly, but normally you use str(person).

---

### `print()` and `__str__`

`print(person)` normally converts person to a string before displaying it.

Therefore, `print(person)` uses the object's `__str__` representation.

`str(person)` returns a string.
`print(person)` displays the string representation but itself returns None.

- Core mental model:

    * Dunder methods tell Python how your custom objects should behave when they are used with Python's built-in operations and syntax.

    * The important idea is not that dunder methods are functions you normally call yourself.

>Instead:
>You define the special method → Python recognizes it → normal Python syntax/functions can automatically use that behavior.

```python
class Person:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"Person: {self.name}"


person = Person("Alice")

str(person)     # "Person: Alice"
print(person)   # Person: Alice
```

---

### Dunder table

- Python syntax → Dunder method : 

| Python syntax / operation | Dunder method            | Purpose                        |
| ------------------------- | ------------------------ | ------------------------------ |
| `Class(...)`              | `__new__`                | Create a new instance          |
| `Class(...)`              | `__init__`               | Initialize an instance         |
| `str(x)` / `print(x)`     | `__str__`                | Human-readable representation  |
| `repr(x)`                 | `__repr__`               | Developer/debug representation |
| `x + y`                   | `__add__`                | Addition                       |
| `x - y`                   | `__sub__`                | Subtraction                    |
| `x * y`                   | `__mul__`                | Multiplication                 |
| `x / y`                   | `__truediv__`            | True division                  |
| `x // y`                  | `__floordiv__`           | Floor division                 |
| `x % y`                   | `__mod__`                | Modulo                         |
| `x ** y`                  | `__pow__`                | Exponentiation                 |
| `-x`                      | `__neg__`                | Unary negation                 |
| `+x`                      | `__pos__`                | Unary positive                 |
| `x == y`                  | `__eq__`                 | Equality                       |
| `x != y`                  | `__ne__`                 | Inequality                     |
| `x < y`                   | `__lt__`                 | Less than                      |
| `x <= y`                  | `__le__`                 | Less than or equal             |
| `x > y`                   | `__gt__`                 | Greater than                   |
| `x >= y`                  | `__ge__`                 | Greater than or equal          |
| `len(x)`                  | `__len__`                | Number of elements / length    |
| `x[i]`                    | `__getitem__`            | Get an item                    |
| `x[i] = y`                | `__setitem__`            | Set an item                    |
| `del x[i]`                | `__delitem__`            | Delete an item                 |
| `x in y`                  | `__contains__`           | Membership testing             |
| `for i in x`              | `__iter__`               | Make object iterable           |
| `next(x)`                 | `__next__`               | Get next item from iterator    |
| `x()`                     | `__call__`               | Make an object callable        |
| `with x`                  | `__enter__` / `__exit__` | Context manager                |
| `bool(x)`                 | `__bool__`               | Truth-value testing            |
| `hash(x)`                 | `__hash__`               | Hashing                        |
| `x & y`                   | `__and__`                | Bitwise AND                    |
| `x \| y`                  | `__or__`                 | Bitwise OR                     |
| `x ^ y`                   | `__xor__`                | Bitwise XOR                    |
| `x << y`                  | `__lshift__`             | Left bit shift                 |
| `x >> y`                  | `__rshift__`             | Right bit shift                |

---

## 1.`__init__` — initialize an object

Probably the most commonly encountered dunder method.

```python
class User:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

# When class is initialized:

user = User("Alice", 25)

# Python calls the initialization machinery and your __init__ method with the new instance:

user.__init__("Alice", 25)
```

Conceptually object creation involves `__new__` as well.

- Purpose:

`__init__` sets up the object's initial state.

```python
class Car:
    def __init__(self, brand: str):
        self.brand = brand
        self.speed = 0
```

---

## 2.`__str__` — human-readable representation

Controls what happens when you use:

`str(object)` or `print(object)`

Example:

```python
class User:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

    def __str__(self) -> str:
        return f"{self.name}, {self.age}"

user = User("Alice", 25)
print(user)

# Output: Alice, 25
```

Without `__str__`, you typically get something like:

`<__main__.User object at 0x...>`

- Convention: __str__ should produce a readable description intended for users.

---

## 3.`__repr__` — developer representation

```python
class User:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

    def __repr__(self) -> str:
        return f"User(name={self.name!r}, age={self.age!r})"

# Now:

user = User("Alice", 25)

print(repr(user))

# Gives:

User(name='Alice', age=25)
```

Think str vs repr:

`__str__`  → for humans
`__repr__` → for developers/debugging

For example:

```python
print(user)

might produce:

Alice, 25

# while:

repr(user)

# might produce:

# User(name='Alice', age=25)
```

---

## 4.`__len__` — make `len()` work

Suppose you create a class representing a playlist:

```python
class Playlist:
    def __init__(self, songs: list[str]):
        self.songs = songs

    def __len__(self) -> int:
        return len(self.songs)

# Now

playlist = Playlist(["Song A", "Song B", "Song C"])

print(len(playlist))

# Output: 3
```

Python essentially translates:

`len(playlist)` -> into the appropriate special-method machinery, invoking your object's `__len__`.

## 5.`__eq__` — equality with `==`

You can define what == means for your class.

```python
class User:
    def __init__(self, user_id: int):
        self.user_id = user_id

    def __eq__(self, other) -> bool:
        return self.user_id == other.user_id

# Now:

alice = User(1)
bob = User(1)

print(alice == bob)

# Result: True
```

Without `__eq__`, custom objects generally compare based on object identity rather than your desired domain-specific equality.

---

## 6.Comparison methods

Python has several dunder methods for comparisons:

```text
__eq__   # ==
__ne__   # !=
__lt__   # <
__le__   # <=
__gt__   # >
__ge__   # >=
```

- Example:

```python
class Product:
    def __init__(self, price: float):
        self.price = price

    def __lt__(self, other) -> bool:
        return self.price < other.price

# Then:

a = Product(10)
b = Product(20)

print(a < b)

# Output: True
```

---

## 7.Arithmetic dunder methods

You can define how operators work with your class.

For example:

```python
__add__   # +
__sub__   # -
__mul__   # *
__truediv__ # /
```

- Example:

```python
class Point:
    def __init__(self, x: int, y: int):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Point(
            self.x + other.x,
            self.y + other.y
        )

# Now:

a = Point(1, 2)
b = Point(3, 4)

c = a + b

# Python effectively uses:

a.__add__(b)

# And:

c.x  # 4
c.y  # 6

# This is called operator overloading.
```

---

## 8.`__getitem__` — make `[]` work

You can make your object behave like a collection.

```python
class Playlist:
    def __init__(self, songs: list[str]):
        self.songs = songs

    def __getitem__(self, index: int) -> str:
        return self.songs[index]

# Now:

playlist = Playlist(["Song A", "Song B", "Song C"])

print(playlist[0])

# Output: Song A

# Python uses:

playlist.__getitem__(0)

# You can also support slices:

playlist[0:2]

# because the value passed to __getitem__ can be a slice object.
```

---

## 9.`__setitem__`

This controls:

- `object[key] = value`

- Example:

```python
class Inventory:
    def __init__(self):
        self.items = {}

    def __setitem__(self, key: str, value: int) -> None:
        self.items[key] = value

# Now:

inventory = Inventory()

inventory["apples"] = 10

# Python invokes the object's __setitem__.
```

---

## 10.`__contains__` — in

You can define what happens with:

`x in object`

- Example:

```python
class Team:
    def __init__(self, members: list[str]):
        self.members = members

    def __contains__(self, name: str) -> bool:
        return name in self.members

# Now:

team = Team(["Alice", "Bob"])

print("Alice" in team)

# Output: True
```

---


## 11.`__iter__` — make an object iterable

This is particularly important.

- Suppose:

```python
class Team:
    def __init__(self, members: list[str]):
        self.members = members

"""
You want:

for member in team:
    print(member)
"""

# Define:

class Team:
    def __init__(self, members: list[str]):
        self.members = members

    def __iter__(self):
        return iter(self.members)

# Now:

team = Team(["Alice", "Bob", "Charlie"])

for member in team:
    print(member)

# works.
```

----

## 12.`__call__` — make an object callable

Normally: `function()` works because it's a function.

But you can make a class instance behave like a function:

```python
class Multiplier:
    def __init__(self, factor: int):
        self.factor = factor

    def __call__(self, value: int) -> int:
        return value * self.factor

# Now:

double = Multiplier(2)

print(double(5))

# Output: 10

# Python invokes:

double.__call__(5)

# This can be useful for callable objects, decorators, and stateful function-like objects.
```

---

## 13.`__enter__` and `__exit__` — with

These power the context-manager protocol.

When you write:

with something:
    ...

Python uses:

__enter__
__exit__

Example:

```python
class Connection:
    def __enter__(self):
        print("Opening connection")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Closing connection")

# Then:

with Connection() as connection:
    print("Doing work")
```

Conceptually:

```text
__enter__()
    ↓
run body
    ↓
__exit__()
```

This is commonly used for resource management such as files, database connections, locks, etc.

---

## 14.`__new__` vs `__init__`

This distinction is worth knowing.

__new__  → creates the instance
__init__ → initializes the instance

For most classes, only required: `__init__`

- Example:

```python
class User:
    def __init__(self, name: str):
        self.name = name
```

`__new__` is generally reserved for more advanced cases such as:
 
 - immutable types

 - custom object creation
  
 - metaclasses

 - singleton-like patterns
 
 - subclasses of str, int, tuple, etc.

Don't use `__new__` when `__init__` is sufficient.

---

## `__name__ == "__main__"`

This is one of the most common Python patterns. The key idea is:

>"Only run this code if this Python file is being run directly, not if it is being imported."

- `__name__` is a special Python variable that Python automatically creates for every module.

Its value depends on how the file is being used.

1.Running the file directly

If you have hello.py and run: `python hello.py`

Python sets:
```python
__name__ = "__main__"
```

Therefore:
```python
if __name__ == "__main__":
```
is `True`, so the code inside runs.

---

2.Importing the file

Suppose another file does:

```python
import hello
```

Now Python sets hello.py's:

```python
__name__ = "hello"
```

So:

```python
if __name__ == "__main__":
```

becomes `False` and the code inside the if doesn't run.

---

### Example

```python
# hello.py

def greet(name):
    return f"Hello, {name}"


if __name__ == "__main__":
    print(greet("Alice"))
```

If called in the terminal directly:

```bash
python hello.py

# output: Hello, Alice
```

If another file imports it:

```python
import hello
```

the `greet()` function becomes available, but the `print()` doesn't execute.

---

### Mental model

Mental model

Think of: `if __name__ == "__main__"` as:

"Am I the main program being executed right now?"

>Yes → run the code inside.

>No, I'm being imported → don't run the code inside.

And `__name__`is another example of a dunder name: Python provides it automatically as part of its module system.