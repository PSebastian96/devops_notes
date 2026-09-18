# Tips

## Quick reference

| Long form                | Python shorthand           |
| ------------------------ | -------------------------- |
| `x = x + 1`              | `x += 1`                   |
| `if/else` assignment     | `x if condition else y`    |
| Build list with loop     | `[x for x in items]`       |
| Dictionary loop          | `{k: v for k, v in items}` |
| `dict` default check     | `d.get("key", default)`    |
| Swap variables           | `a, b = b, a`              |
| Loop with index          | `enumerate(items)`         |
| Loop through two lists   | `zip(a, b)`                |
| Check all                | `all(...)`                 |
| Check any                | `any(...)`                 |
| Remove duplicates        | `set(items)`               |
| Assign inside expression | `:=`                       |

---

## shorthands

### Ternary operator

```python
# instead
if age >= 18:
    status = "adult"
else:
    status = "minor"

# use
status = "adult" if age >= 18 else "minor"
```

### Multiple assignment

```python
# instead
x = 10
y = 20
z = 30

# use
x, y, z = 10, 20, 30
```

### Operators

```python
# instead
x = x + 1


# use
x += 1
x -= 1
x *= 2
x /= 2
x **= 2
x //= 2
x %= 2
```

### List comprehensions

```python
# instead

squares = []

for x in range(10):
    squares.append(x ** 2)

# use
squares = [x ** 2 for x in range(10)]

# with condition (if)
even = [x for x in range(10) if x % 2 == 0]
```

### Dictionary comprehensions

```python
# instead
squares = {}

for x in range(5):
    squares[x] = x ** 2

# use
squares = {x: x ** 2 for x in range(5)}
```

### if shorthand

```python
instead
if name:
    print("Hello")

# use
if name: print("Hello")
```

### or for default values

```python
# instead
if name:
    username = name
else:
    username = "Guest"

# use
username = name or "Guest"
```

### get() for dictionaries

```python
if "name" in user:
    name = user["name"]
else:
    name = "Unknown"

# use
name = user.get("name", "Unknown")
```

### enumerate()

```python
i = 0

for item in items:
    print(i, item)
    i += 1

# use
for i, item in enumerate(items):
    print(i, item)
```

### lambda function

```python
def square(x):
    return x ** 2

# use
square = lambda x: x ** 2
print(square(5))
```

### `*` unpacking

```python
numbers = [1, 2, 3]
a = numbers[0]
b = numbers[1]
c = numbers[2]

# use
a, b, c = numbers

# or
a, *rest = [1, 2, 3, 4, 5]

print(a)       # 1
print(rest)    # [2, 3, 4, 5]
```

### Set shorthand

```python
numbers = [1, 2, 2, 3, 3, 3]

unique = list(set(numbers))
print(unique)
# [1, 2, 3]
```

### all() and any()

```python
# Checks whether every value is positive.
all_positive = all(x > 0 for x in numbers)

# Checks whether at least one value is negative.
has_negative = any(x < 0 for x in numbers)
```

---

## Walrus operator

The walrus operator, also known as the assignment expression operator :=, was introduced in Python 3.8. It allows you to assign values to variables as part of an expression.

Variable `:=` expression.

1.Assignment in While Loops:

One common use case of the walrus operator is within a while loop condition, where you need to both evaluate an expression and assign its value to a variable.

```python
# Example: Read lines from a file until a certain condition is met
while (line := file.readline().strip()) != "":
    # Process each line
    print(line)
```

2.Assignment in List Comprehensions:

You can use the walrus operator in list comprehensions to avoid computing the same value multiple times.

```python
# Example: Filter a list and simultaneously compute a value
numbers = [1, 2, 3, 4, 5]
squared_even = [x ** 2 for x in numbers if (x % 2 == 0) := True]
```

3.Assignment in Conditional Expressions:

The walrus operator can be used in conditional expressions to avoid redundant computation.

```python
# Example: Check if a value exists in a dictionary and get it if present
if (value := my_dict.get(key)) is not None:
    # Process the value
    print(value)
```

4.Improving Readability:

In some cases, using the walrus operator can improve code readability by making it more concise and expressive.

```python
# Example: Read a line and check if it's not empty
while (line := file.readline().strip()):
    # Process each non-empty line
    print(line)
```

---

## f string functions

### Separators

```python
n = 10000000
print(f'dash separator: {n:_}')
print(f'comma separator: {n:,}')

# dash separator: 10_000_000
# comma separator: 10,000,000
```

### Datetime

```python
from datetime import datetime

# Define variables with type annotations
now: datetime = datetime.now()
current_date: str = now.strftime("%Y-%m-%d")
current_time: str = now.strftime("%H:%M:%S")
weekday: str = now.strftime("%A")
month: str = now.strftime("%B")
year: int = now.year
day_of_month: int = now.day

# Print the variables
print(f"Current date: {current_date}")
print(f"Current time: {current_time}")
print(f"Weekday: {weekday}")
print(f"Month: {month}")
print(f"Year: {year}")
print(f"Day of the month: {day_of_month}")
Current date: 2024-04-22
Current time: 15:52:35
Weekday: Monday
Month: April
Year: 2024
Day of the month: 22
```

### Decimals

```python
n: float = 1234.5678

# Format the float variable n with different precision and formatting options
print(f'Result with 3 decimal places: {n:.3f}')
print(f'Result with 2 decimal places: {n:.2f}')
print(f'Result without decimal places (rounds to the nearest integer): {n:.0f}')
print(f'Result with comma as thousands separator and 2 decimal places: {n:,.2f}')
Result with 3 decimal places: 1234.568
Result with 2 decimal places: 1234.57
Result without decimal places (rounds to the nearest integer): 1235
Result with comma as thousands separator and 2 decimal places: 1,234.57
```

### Positions

```python
# Left Alignment (<):
# Syntax: f"{text:<width}"
""" Explanation: The < specifier left-aligns the text within a field of the specified width. Any extra space is added to the right of the text.
Use Case: Left-aligning text is commonly used for creating columns of data or formatting text in tables.
"""
name = "John"
age = 30
city = "New York"
print(f"{'Name:':<10} {name}")
print(f"{'Age:':<10} {age}")
print(f"{'City:':<10} {city}")

# Name:      John
# Age:       30
# City:      New York
# Right Alignment (>):

# Syntax: f"{text:>width}"
""" Explanation: The > specifier right-aligns the text within a field of the specified width. Any extra space is added to the left of the text.
Use Case: Right-aligning text is useful for creating formatted reports or aligning numeric data.
"""

total = 100
discount = 10
tax = 5
print(f"Total: {total:>8}")
print(f"Discount: {discount:>8}")
print(f"Tax: {tax:>8}")

# Total:      100
# Discount:       10
# Tax:        5
# Center Alignment (^):

# Syntax: f"{text:^width}"
""" Explanation: The ^ specifier centers the text within a field of the specified width. Any extra space is divided equally on both sides of the text.
Use Case: Center-aligning text is useful for creating visually appealing headers or titles.
"""

title = "Welcome"
print(f"|{title:^20}|")
# |      Welcome       |
```

### String truncating

```python
"""
Truncate from the Left (:<width):
Syntax: f"{text:<width}"
Explanation: This truncates the text from the right to fit within the specified width. If the text is longer than the specified width, it is truncated from the right.
Use Case: Truncating text from the left is useful for displaying the beginning of a long string.
"""
long_text = "This is a long text that needs to be truncated"
print(f"Truncated: {long_text:<20}")

""" Truncated: This is a long text that needs to be truncated
Truncate from the Right (:>width):
Syntax: f"{text:>{width}}"
Explanation: This truncates the text from the left to fit within the specified width. If the text is longer than the specified width, it is truncated from the left.
Use Case: Truncating text from the right is useful for displaying the end of a long string.
"""

long_text = "This is a long text that needs to be truncated"
print(f"Truncated: {long_text:>20}")
# Truncated: This is a long text that needs to be truncated

# Custom Truncating
long_text = "This is a long text that needs to be truncated"
max_width = 20

# Check if text length exceeds the maximum width
if len(long_text) > max_width:
    # Calculate how much text to keep from each side
    chars_to_keep = max_width - 3  # Keep 3 characters for ellipsis (...)

    # Truncate the text from both sides and add ellipsis in the middle
    truncated_text = long_text[:chars_to_keep // 2] + "..." + long_text[-chars_to_keep // 2:]

    print(f"Truncated: {truncated_text}")
else:
    print(f"Truncated: {long_text}")


print(f'Second truncate: {long_text:<15.5}')
print(f'Third truncate: {long_text:<15.10}')
print(f'Fourth truncate: {long_text:<15.15}')
print(f'Fifth truncate: {long_text:<15.25}')

# Truncated: This is ...truncated
# Second truncate: This           
# Third truncate: This is a      
# Fourth truncate: This is a long 
# Fifth truncate: This is a long text that 
```

---

## map

The `map()` function in Python is a built-in function used to apply a given function to each item of an iterable (such as a list, tuple, or set) and returns a map object, which is an iterator. The `map()` function takes two arguments: the function to apply and the iterable(s) to apply it to.

map(function, iterable1, iterable2, ...)

- function: The function to apply to each item of the iterable(s).

- iterable1, iterable2, etc.: One or more iterables (e.g., lists, tuples) whose items will be passed to the function.

- `map()`: function applies the given function to each item in the input iterables and returns an iterator that yields the results of applying the function to the items.

EXAMPLES:

```python
# Define a function to calculate the square of a number
def square(x):
    return x ** 2

# Create a list of numbers
numbers = [1, 2, 3, 4, 5]

# Use map() to apply the square function to each number in the list
squared_numbers = map(square, numbers)

# Convert the map object to a list to see the results
print(list(squared_numbers))  # Output: [1, 4, 9, 16, 25]
[1, 4, 9, 16, 25]
# example of normal for loop
my_pets = ['alfred', 'tabitha', 'william', 'arla']
uppered_pets = []

for pet in my_pets:
    pet_ = pet.upper()
    uppered_pets.append(pet_)

print(f'For loop result: {uppered_pets}')

# example with map() function
my_pets1 = ['alfred', 'tabitha', 'william', 'arla']

map_pets1 = list(map(str.upper, my_pets1))

print(f'map() result: {map_pets1}')
For loop result: ['ALFRED', 'TABITHA', 'WILLIAM', 'ARLA']
map() result: ['ALFRED', 'TABITHA', 'WILLIAM', 'ARLA']
circle_areas = [3.56773, 5.57668, 4.00914, 56.24241, 9.01344, 32.00013]

#first example 
result_one = list(map(round, circle_areas, range(1, 7)))

print(f'Result one: {result_one}')

# second example
result_two = list(map(round, circle_areas, range(1, 3)))

print(f'Result two: {result_two}')

# Result one: [3.6, 5.58, 4.009, 56.2424, 9.01344, 32.00013]
# Result two: [3.6, 5.58]
```

---

## zip

The zip() function in Python is a built-in function used to combine multiple iterables (such as lists, tuples, or sets) element-wise. It takes iterables as input and returns an iterator of tuples where each tuple contains elements from the input iterables at the same index.

zip(iterable1, iterable2, ...)

iterable1, iterable2, etc.: The iterables (e.g., lists, tuples, sets) to be zipped together.
zip(): function iterates through the input iterables in parallel and creates tuples by pairing up elements from corresponding positions. If the input iterables are of different lengths, zip() stops when the shortest iterable is exhausted.
The zip() function is useful for tasks such as iterating over multiple iterables simultaneously, performing parallel operations on multiple datasets, and creating dictionaries from two lists. It provides a concise way to work with multiple iterables in a coordinated manner

EXAMPLES:

```python
# Two lists
names = ['Alice', 'Bob', 'Charlie']
ages = [30, 25, 35]

# Use zip() to combine the lists element-wise
combined = zip(names, ages)

# Convert the zip object to a list to see the results
print(list(combined))  # Output: [('Alice', 30), ('Bob', 25), ('Charlie', 35)]
# [('Alice', 30), ('Bob', 25), ('Charlie', 35)]

a = ("John", "Charles", "Mike")
b = ("Jenny", "Christy", "Monica", "Vicky")

x = zip(a, b)

print(tuple(x))
# (('John', 'Jenny'), ('Charles', 'Christy'), ('Mike', 'Monica'))

my_strings = ['a', 'b', 'c', 'd', 'e']
my_numbers = [1, 2, 3, 4, 5]

results = list(map(lambda x, y: (x, y), my_strings, my_numbers))

print(results)
# [('a', 1), ('b', 2), ('c', 3), ('d', 4), ('e', 5)]

# Two lists
names = ['Alice', 'Bob', 'Charlie']
ages = [30, 25, 35]

# Use zip() to combine the lists element-wise
combined = zip(names, ages)

# Convert the zip object to a list to see the results
print(list(combined))  # Output: [('Alice', 30), ('Bob', 25), ('Charlie', 35)]
# [('Alice', 30), ('Bob', 25), ('Charlie', 35)]
```

---

## filter

The `filter()` function in Python is a built-in function used to filter elements from an iterable based on a specified condition or predicate function. It takes two arguments: the predicate function and the iterable to be filtered.

- `filter(function, iterable)`

- function: The predicate function that determines whether each element of the iterable should be included in the output.

- iterable: The iterable (e.g., list, tuple, set) from which elements will be filtered.

The `filter()` function applies the predicate function to each element in the iterable. If the predicate function returns True for an element, the element is included in the output, otherwise, it is excluded.

The `filter()` function is commonly used to selectively extract elements from iterables based on specific criteria or conditions. It provides a concise and readable way to perform filtering operations without the need for explicit loops.

```python
# Predicate function to check if a number is even
def is_even(x):
    return x % 2 == 0

# List of numbers
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Use filter() to filter even numbers from the list
even_numbers = filter(is_even, numbers)

# Convert the filter object to a list to see the results
print(f'Even numbers: {list(even_numbers)}')  # Output: [2, 4, 6, 8, 10]
Even numbers: [2, 4, 6, 8, 10]
scores = [66, 90, 68, 59, 76, 60, 88, 74, 81, 65]

def is_A_student(score):
    return score > 75

over_75 = list(filter(is_A_student, scores))

print(f' Scores: {over_75}')
# Scores: [90, 76, 88, 81]

dromes = ("demigod", "rewire", "madam", "freer", "anutforajaroftuna", "kiosk")

palindromes = list(filter(lambda word: word == word[::-1], dromes))

print(f' Palindromes: {palindromes}')
# Palindromes: ['madam', 'anutforajaroftuna']
```

---

## reduce

The `reduce()` function in Python is a part of the functools module and is used to apply a rolling computation to sequential pairs of values in an iterable, optionally with an initial value. It repeatedly applies the given function to the elements of the iterable, accumulating the result, and returns a single value.

- `reduce(func, iterable[, initial])`

- function: The function that will be applied to the elements of the iterable. It should take two arguments and return a single value.

- iterable: The iterable (e.g., list, tuple) to be reduced.

- initial (optional): An initial value to start the computation. If provided, the function will be applied with this initial value and the first element of the iterable.

It's important to note that the `reduce()` function is not a built-in function in Python 3.x, unlike in Python 2.x. Instead, it's available in the functools module, so it needs to be imported before using it.

The `reduce()` function is useful for performing rolling computations or aggregations over elements of an iterable, such as computing the sum, product, maximum, or minimum of a sequence. However, since it's not as commonly used as functions like map() and filter(), it's generally recommended to use built-in functions or list comprehensions for simpler aggregation tasks to improve code readability.

```python
import functools

# List of numbers
numbers = [1, 2, 3, 4, 5]

# Compute the sum of numbers using reduce()
sum_result = functools.reduce(lambda x, y: x + y, numbers)

print(sum_result)  # Output: 15 (1 + 2 + 3 + 4 + 5 = 15)
# 15
```

```python
from functools import reduce

numbers = [3, 4, 6, 9, 34, 12]

def custom_sum(first, second):
    return first + second

result = reduce(custom_sum, numbers)
print(result)
# 68
```

```python
from functools import reduce

numbers = [3, 4, 6, 9, 34, 12]

def custom_sum(first, second):
    return first + second

result = reduce(custom_sum, numbers, 10)
print(result)

# The result is 78 because reduce, initially, uses 10 as the first argument to custom_sum.
# 78
```

```python
import functools

# List of numbers
numbers = [5, 2, 8, 1, 9, 3]

# Compute the maximum number using reduce()
max_number = functools.reduce(lambda x, y: x if x > y else y, numbers)

print(max_number)  # Output: 9 (maximum element in the list)
# 9
```