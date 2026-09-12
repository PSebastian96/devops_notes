# 🐍 Python Syntax

## Comments

- `#` is used before single line comment.
- `'''  '''` is used for multi line commments.
- `""" """` is also used for multi line comments.

## Naming convention

camelCase : myVar

snake-case : my-var

UPPER_CASE : MY_VAR

under_score : my_var (Variable or function names)

PasqualCase : MyVar (Class names)

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

x	NOT x
False	True
True	False

- Logical AND Operator: Returns a value/executes statement if BOTH conditions are TRUE.

x	y	x AND Y
False	False	FALSE
False	False	FALSE
True	False	FALSE
True	True	TRUE

- Logical OR Operator: Returns a value/executes statement if ONE OR BOTH condition(s) is/are TRUE.

x	y	x OR Y
False	False	FALSE
False	True	TRUE
True	False	TRUE
True	True	TRUE

Logical Operator Priority:

1. NOT
2. AND
3. OR

---

IS vs ==

---

## Syntax Examples

```python
# variables

# functions

# classes

# list

# tuple

# dictionary
```