# 🐍 Python Syntax

## Comments

- `#` is used before single line comment.
- `'''  '''` is used for multi line commments.
- `""" """` is also used for multi line comments.

---

## Naming convention

camelCase : myVar

snake-case : my-var

UPPER_CASE : MY_VAR

under_score : my_var (Variable or function names)

PasqualCase : MyVar (Class names)

---

## 📋 Summary Table of Reserved Keywords

| **Category**           | **Keywords**                                                                 |
|------------------------|------------------------------------------------------------------------------|
| **Control Flow**       | `if`, `else`, `elif`, `while`, `for`, `break`, `continue`, `pass`            |
| **Boolean Logic**      | `True`, `False`, `not`, `and`, `or`                                          |
| **Functions**          | `def`, `return`, `lambda`, `yield`, `nonlocal`                               |
| **Exception Handling** | `try`, `except`, `finally`, `raise`, `assert`                                |
| **Classes & OOP**      | `class`, `self`, `super`, `is`                                               |
| **Variable Scope**     | `global`, `nonlocal`, `del`                                                  |
| **Imports/Modules**    | `import`, `from`, `as`                                                       |
| **Data Definitions**   | `None`, `in`                                                                 |
| **Async & Await**      | `async`, `await`                                                             |
| **Context Management** | `with`                                                                       |
| **Structural Matching**| `match`, `case`                                                              |

---

## Basic Operations

### Flow of Execution & Operations

- Statements are executed from LEFT to RIGHT.

- Flow of execution of functions start at TOP to BOTTOM.

- Sometimes a function calls another function, therefore sometimes it makes more sense to follow the flow of execution of function(s).

---

### PEMDAS

Order of Operations:

1. PE () - Parenthesus has the first precedence.
2. Exponentiation (e^2) is the second precedence.
3. Multiplication (x) & (/) Division have the third precedence.
4. Additon (+) & (-) Subtraction have the last precedence.

---

### Math Operators

|Operators	|Operation | Example |
|-----------|----------|---------|
|`**`| Exponent | 2**3 = 8       |
|`%` | Modulus/Remainder | 22 % 8 = 6 |  
|`//`| Floor Division | 22 // 8 = 2 |
|`/` | Division | 22 / 8 = 2.75 |
|`*` | Multiplication | 3 * 3 = 9| 
|`-` | Subtraction | 5 - 2 = 3 |
|`+` | Addition | 2 + 2 = 4 |
|`+=`| Increment | 2 += 2 = 4 |
|`-=`| Decrement | 4 -= 2 = 2 |

---

### Comparison Operators

| Sign | Meaning | Example |
|------|---------|---------|
|`<` | Less Than| x < y |
|`>` | More Than| x > y |
|`<=`|  Less Than or Equal To| x <= y |
|`>=`|  More Than or Equal To| x >= y |
|`==`|  Equal To| x == y |
|`!=`|  Not Equal To| x != y|

---

### Logical Operators

- Logical NOT Operator: Returns TRUE if the operand is false and vice versa.

| `x`     | `NOT x` |
| ------- | ------- |
| `False` | `True`  |
| `True`  | `False` |


- Logical AND Operator: Returns a value/executes statement if BOTH conditions are TRUE.

| `x`     | `y`     | `x AND y` |
| ------- | ------- | --------- |
| `False` | `False` | `False`   |
| `False` | `True`  | `False`   |
| `True`  | `False` | `False`   |
| `True`  | `True`  | `True`    |


- Logical OR Operator: Returns a value/executes statement if ONE OR BOTH condition(s) is/are TRUE.

| `x`     | `y`     | `x OR y` |
| ------- | ------- | -------- |
| `False` | `False` | `False`  |
| `False` | `True`  | `True`   |
| `True`  | `False` | `True`   |
| `True`  | `True`  | `True`   |

Logical Operator Priority:

1. NOT
2. AND
3. OR

---

## `is` vs `==`

> `==` checks whether two values are equal.
> `is` checks whether two variables refer to the exact same object in memory.

| Operator | Checks          | Example  |
| -------- | --------------- | -------- |
| `==`     | Value equality  | `a == b` |
| `is`     | Object identity | `a is b` |

```python
a = [1, 2, 3]
b = [1, 2, 3]

a == b   # True  → same values
a is b   # False → different objects
```

---

## Syntax Examples

```python
# variables
name = "Alice"
age = 30
is_active = True

print(name)
# Alice
print(age)
# 30
print(is_active)
# True

# functions
def greet(name):
    return f"Hello, {name}!"


message = greet("Alice")
print(message)

# classes
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hello, I'm {self.name}."


person = Person("Alice", 30)

print(person.name)
print(person.age)
print(person.greet())

# list
fruits = ["apple", "banana", "orange"]

print(fruits[0])

fruits.append("mango")

print(fruits)

# tuple
coordinates = (51.5074, -0.1278)

print(coordinates[0])
print(coordinates[1])

# dictionary
person = {
    "name": "Alice",
    "age": 30,
    "is_active": True,
}

print(person["name"])
print(person["age"])
```

| Concept        | Example               | Main characteristic           |
| -------------- | --------------------- | ----------------------------- |
| **Variable**   | `name = "Alice"`      | Stores a value                |
| **Function**   | `def greet():`        | Reusable block of logic       |
| **Class**      | `class Person:`       | Blueprint for objects         |
| **List**       | `["apple", "banana"]` | Ordered, mutable collection   |
| **Tuple**      | `("apple", "banana")` | Ordered, immutable collection |
| **Dictionary** | `{"name": "Alice"}`   | Key-value collection          |