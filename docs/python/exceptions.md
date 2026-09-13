# Error Handling & Exceptions

## Overview

Error handling lets your program deal with problems without crashing unexpectedly.

The main keywords are:

`try` → code that might fail (Keep the try block focused on the operation that can actually fail.)

`except` → what to do if it fails

`else` → runs if no error occurred

`finally` → runs no matter what

!!! tip "Key rules to remember"

    - Catch specific exceptions whenever possible.
    - Use else for code that should run only after successful try.
    - Use finally for cleanup that must happen regardless.
    - Use raise to deliberately report invalid conditions.
    - Don't silently use except: pass.
    - Keep try blocks small and focused.
    - Exceptions are normal tools for handling unexpected runtime situations—not something to fear.

- Syntax:

```python
try:
    # code that might cause an error

except SomeError:
    # handle the error

else:
    # runs if there was no error

finally:
    # always runs
```

- Common Exceptions:

| Exception           | Usually happens when             |
| ------------------- | -------------------------------- |
| `ValueError`        | Wrong value/type of input        |
| `TypeError`         | Incompatible types               |
| `ZeroDivisionError` | Dividing by zero                 |
| `IndexError`        | Invalid list/sequence index      |
| `KeyError`          | Missing dictionary key           |
| `FileNotFoundError` | File doesn't exist               |
| `NameError`         | Variable doesn't exist           |
| `AttributeError`    | Object doesn't have an attribute |
| `ImportError`       | Import fails                     |
| `Exception`         | General base exception           |

---

## Example

```python
try:
    number = int(input("Enter a number: "))
    result = 100 / number

except ValueError:
    print("Please enter a number.")

except ZeroDivisionError:
    print("Cannot divide by zero.")

else:
    print("Result:", result)

finally:
    print("Done.")
```

---

## When to use else

Use it for code that should only execute if the risky operation succeeded.

```python
try:
    file = open("data.txt")
except FileNotFoundError:
    print("File not found.")
else:
    print("File opened successfully.")
```

---

## Multiple Exceptions

Several exceptions can be handled with one except:

```python
try:
    number = int(input("Number: "))
    result = 10 / number

except (ValueError, ZeroDivisionError):
    print("Invalid operation.")
```

Or handle them separately:

```python
try:
    number = int(input("Number: "))
    result = 10 / number

except ValueError:
    print("Invalid number.")

except ZeroDivisionError:
    print("Cannot divide by zero.")
```

Separate handlers are usually clearer when the responses are different.

---

## Custom Error Exception

You can create your own exception types.

```python
class InsufficientFundsError(Exception):
    pass
```

Then:

```python
def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError("Not enough money.")

    return balance - amount
```

Handle it:

```python
try:
    withdraw(100, 200)

except InsufficientFundsError as error:
    print(error)
```

Custom exceptions become especially useful in larger applications.