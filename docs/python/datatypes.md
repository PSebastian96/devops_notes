# Data types

## Overview

In total, the built-in types are commonly listed as:

```text
int
float
complex
bool
str
list
tuple
range
dict
set
frozenset
bytes
bytearray
memoryview
NoneType
```

>Note: Python also has many other types provided by the standard library, such as datetime, Decimal, and Path, plus types you can create yourself using classes.

---

| Datatype   | Characteristics          | Mutable? | Example            | Main use                            |
| ---------- | ------------------------ | -------: | ------------------ | ----------------------------------- |
| `int`      | Whole numbers            |        ❌ | `10`               | Counting, calculations              |
| `float`    | Decimal numbers          |        ❌ | `3.14`             | Measurements, calculations          |
| `bool`     | `True` / `False`         |        ❌ | `True`             | Conditions / logic                  |
| `str`      | Text, ordered characters |        ❌ | `"Hello"`          | Text                                |
| `list`     | Ordered collection       |        ✅ | `[1, 2, 3]`        | Collection of items that may change |
| `tuple`    | Ordered collection       |        ❌ | `(1, 2, 3)`        | Fixed collection of items           |
| `dict`     | Key → value pairs        |        ✅ | `{"name": "John"}` | Structured data                     |
| `set`      | Unique, unordered values |        ✅ | `{1, 2, 3}`        | Removing duplicates, membership     |
| `NoneType` | Represents no value      |        — | `None`             | Missing/empty result                |

- Mutable means you can change the object after creating it.

- Imutable means it can **not** be changed after creating it.

---

## 🔢 Numeric types

| Type      | Example         | Description     |
| --------- | --------------- | --------------- |
| `int`     | `42`            | Whole numbers   |
| `float`   | `3.14`          | Decimal numbers |
| `complex` | `2 + 3j`        | Complex numbers |
| `bool`    | `True`, `False` | Boolean values  |

- Methods:

| Operation      | Example  | Result |
| -------------- | -------- | -----: |
| Addition       | `5 + 2`  |    `7` |
| Subtraction    | `5 - 2`  |    `3` |
| Multiplication | `5 * 2`  |   `10` |
| Division       | `5 / 2`  |  `2.5` |
| Floor division | `5 // 2` |    `2` |
| Remainder      | `5 % 2`  |    `1` |
| Power          | `5 ** 2` |   `25` |

- Numerical:

```python
age = 25
price = 19.99

total = price * 2
print(total)       # 39.98
print(age + 5)     # 30
print(10 // 3)     # 3
print(10 % 3)      # 1
```
- Boolean:

```python
age = 20

is_adult = age >= 18

if is_adult:
    print("Adult")
else:
    print("Not an adult")
```

---

## 📝 Text type

| Type  | Example   | Description |
| ----- | --------- | ----------- |
| `str` | `"Hello"` | Text/string |

- Methods:

| Method          | Example                          | Purpose                       |
| --------------- | -------------------------------- | ----------------------------- |
| `.lower()`      | `"HELLO".lower()`                | Lowercase                     |
| `.upper()`      | `"hello".upper()`                | Uppercase                     |
| `.strip()`      | `" hi ".strip()`                 | Remove surrounding whitespace |
| `.replace()`    | `"Hi Bob".replace("Bob", "Sam")` | Replace text                  |
| `.split()`      | `"a,b,c".split(",")`             | Split into a list             |
| `.join()`       | `"-".join(["a","b"])`            | Join strings                  |
| `.find()`       | `"hello".find("e")`              | Find position                 |
| `.startswith()` | `"hello".startswith("he")`       | Check beginning               |
| `.endswith()`   | `"hello".endswith("lo")`         | Check ending                  |

- Code:

```python
# use ' ' or " "
# to use a quote in the string use one mark as main and use the other for qoute 
# indexing/coutning starts with 0,1,2...
# using [:] it is possible to slice letters, text, words 
# [a:b] including the first [a] but excluding the last [b].

# get a letter from the string variable
fruit = 'banana'
letter = fruit[0] # output is b
letter_one = fruit[1] # output is a

# Separate the Monty Python into two words
my_str = 'Monty Python'
my_str[0:5] # Monty#
my_str[6:12] # Python

# If [:a] the slice starts at the beginning of the string. 
# If [a:], the slice goes to the end of the string.
fruit = 'banana'
fruit[:3] # 'ban'
fruit[3:] # 'ana'
fruit[:] # 'banana'
fruit[5:5] # ' '

# dir() function lists all the string methods available
dir(fruit)

# len() function returns the length (number of characters) of the string
text = "Hello, World!"
length = len(text) # 13

# str(object) Converts the specified object to a string
number = 42
text = str(number) # '42'

# format(*args, **kwargs) Formats a string by replacing placeholders with values
# Can be used with positional arguments and keyword arguments
name = "John"
age = 30
message = "My name is {} and I am {} years old.".format(name, age)
# Result: "My name is John and I am 30 years old."

# The find method can find substrings as well as characters
word = 'banana'
index = word.find('a')
print(index) # 1

word.find('na') # 2

# It can take as a second argument the index where it should start
 word.find('na', 3) # 4
```

- F string functions

```python
name = "Alice"
age = 25

print(f"My name is {name} and I am {age}.")
```

---

## 📦 Sequence types

| Type    | Example     | Description                   |
| ------- | ----------- | ----------------------------- |
| `list`  | `[1, 2, 3]` | Ordered, mutable collection   |
| `tuple` | `(1, 2, 3)` | Ordered, immutable collection |
| `range` | `range(5)`  | Sequence of numbers           |

---

### List

| Method       | Example           | Purpose            |
| ------------ | ----------------- | ------------------ |
| `.append()`  | `x.append(4)`     | Add to end         |
| `.insert()`  | `x.insert(1, 5)`  | Add at position    |
| `.extend()`  | `x.extend([4,5])` | Add multiple items |
| `.remove()`  | `x.remove(2)`     | Remove value       |
| `.pop()`     | `x.pop()`         | Remove/return item |
| `.sort()`    | `x.sort()`        | Sort list          |
| `.reverse()` | `x.reverse()`     | Reverse list       |
| `.index()`   | `x.index(5)`      | Find position      |
| `.count()`   | `x.count(5)`      | Count occurrences  |
| `.copy()`    | `x.copy()`        | Make a copy        |
| `.clear()`   | `x.clear()`       | Remove everything  |

- Code:

```python
# Empty List:
empty_list = []

# List with Elements:
numbers = [1, 2, 3, 4, 5]
fruits = ['apple', 'banana', 'orange']
mixed = [1, 'apple', True, [2, 'banana'], None]

# Nested Lists (Lists of Lists):
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# List Comprehension:
squares = [x**2 for x in range(1, 6)]  # [1, 4, 9, 16, 25]

# Using list() Function:
# You can also create a list from an iterable using the list() function:
numbers = list(range(1, 6))  # [1, 2, 3, 4, 5]

# Creating a Copy of a List:
original_list = [1, 2, 3]
copied_list = original_list.copy()  # or list(original_list)

# Examples of list
cheeses = ['Cheddar', 'Edam', 'Gouda']
numbers = [17, 123]
empty = []
# print() --> ['Cheddar', 'Edam', 'Gouda'] [17, 123] []

# Nested list
nest_li = ['spam', 2.0, 5, [10, 20]]

# List is mutable
numbers = [17, 123]
numbers[1] = 5 # [17, 5]

# Since lists are mutable, it is often useful to make a copy before performing operations that fold, spindle, or mutilate lists
# Any integer expression can be used as an index
# If you try to read or write an element that does not exist, you get an IndexError
# If an index has a negative value, it counts backward from the end of the list

# List slicing
# starts with the first index, stops before the second index'es value
t = ['a', 'b', 'c', 'd', 'e', 'f']
t[1:3] # ['b', 'c']

t = ['a', 'b', 'c', 'd', 'e', 'f']
t[:4] # ['a', 'b', 'c', 'd']

t = ['a', 'b', 'c', 'd', 'e', 'f']
t[3:] # ['d', 'e', 'f']

t[:] # ['a', 'b', 'c', 'd', 'e', 'f']

# left to right slicing starts with 0
# Right to left slicing starts at -1
t = ['a', 'b', 'c', 'd', 'e', 'f']
t[-3:-1] # ['d','e']

# list reverse
t = ['a', 'b', 'c', 'd', 'e', 'f']
t[::-1] # ['f', 'e', 'd', 'c', 'b', 'a']

# slice operator on the left side of an assignment can update multiple elements
t = ['a', 'b', 'c', 'd', 'e', 'f']
t[1:3] = ['x', 'y'] # ['a', 'x', 'y', 'd', 'e', 'f']

# len(list):
# Returns the number of elements in the list.
numbers = [1, 2, 3, 4, 5]
length = len(numbers) # Result: 5

# max(list):
# Returns the maximum value in the list.
numbers = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
max_value = max(numbers) # Result: 9

# min(list):
# Returns the minimum value in the list.
numbers = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
min_value = min(numbers) # Result: 1

# sum(iterable):
# Returns the sum of all elements in the list or any iterable.
numbers = [1, 2, 3, 4, 5]
total = sum(numbers) # Result: 15
```

---

### Tuple

| Method     | Example      | Purpose           |
| ---------- | ------------ | ----------------- |
| `.count()` | `x.count(5)` | Count occurrences |
| `.index()` | `x.index(5)` | Find position     |

- Code:

```python
# Tuples can contain duplicates
# Items can be different of data (integer, floats, tuple)
#Tuples can be used as a constant list(not changable), can be used as a dictionary key-more memory efficient.

# Examples of tuples
cheeses = ('Cheddar', 'Edam', 'Gouda')
numbers = (17, 123)
empty = ()

# Empty Tuple:
empty_tuple = ()

# Tuple with Elements:
numbers = (1, 2, 3, 4, 5)
fruits = ('apple', 'banana', 'orange')
mixed = (1, 'apple', True, (2, 'banana'), None)

# Nested Tuples (Tuples of Tuples):
matrix = ((1, 2, 3), (4, 5, 6), (7, 8, 9))

# Tuple Comprehension:
# Tuples do not support comprehension like lists do.
# However, you can create a tuple from a generator expression using the tuple() function:
squares = tuple(x**2 for x in range(1, 6))  # (1, 4, 9, 16, 25)

# Using tuple() Function:
# You can also create a tuple from an iterable using the tuple() function:
numbers = tuple(range(1, 6))  # (1, 2, 3, 4, 5)

# Creating a Copy of a Tuple:
original_tuple = (1, 2, 3)
copied_tuple = original_tuple[:]  # Using slicing to create a copy

# Nested tuple
nest_tup = ('spam', 2.0, 5, (10, 20))

# Tuple is immutable
# numbers = (17, 123)
# numbers[1] = 5  # This will raise a TypeError since tuples are immutable

# Tuple slicing
# Similar to lists, tuples also support slicing
t = ('a', 'b', 'c', 'd', 'e', 'f')

# Slice from index 1 to index 3 (exclusive)
t[1:3]  # ('b', 'c')

# Slice from the beginning to index 4 (exclusive)
t[:4]  # ('a', 'b', 'c', 'd')

# Slice from index 3 to the end
t[3:]  # ('d', 'e', 'f')

# Slice the entire tuple
t[:]  # ('a', 'b', 'c', 'd', 'e', 'f')

# Right-to-left slicing starts at -1
# Slice from index -3 (inclusive) to index -1 (exclusive)
t[-3:-1]  # ('d', 'e')

# Tuple reverse
t[::-1]  # ('f', 'e', 'd', 'c', 'b', 'a')

# Slicing every second value in Tuple
numbers = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12)
result = numbers[1:11:2] # (1, 3, 5, 7, 9)

# len(tuple):
# Returns the number of elements in the tuple.
numbers = (1, 2, 3, 4, 5)
length = len(numbers)  # Result: 5

# max(tuple):
# Returns the maximum value in the tuple.
numbers = (3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5)
max_value = max(numbers)  # Result: 9

# min(tuple):
# Returns the minimum value in the tuple.
numbers = (3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5)
min_value = min(numbers)  # Result: 1

# sum(iterable):
# Returns the sum of all elements in the tuple or any iterable.
numbers = (1, 2, 3, 4, 5)
total = sum(numbers)  # Result: 15
```

---

## 🗂️ Mapping type

| Type   | Example                      | Description     |
| ------ | ---------------------------- | --------------- |
| `dict` | `{"name": "Sam", "age": 20}` | Key-value pairs |

- Methods:

| Method       | Example         | Purpose               |
| ------------ | --------------- | --------------------- |
| `.get()`     | `d.get("name")` | Safely retrieve value |
| `.keys()`    | `d.keys()`      | Get keys              |
| `.values()`  | `d.values()`    | Get values            |
| `.items()`   | `d.items()`     | Get key-value pairs   |
| `.update()`  | `d.update(...)` | Add/update data       |
| `.pop()`     | `d.pop("age")`  | Remove key            |
| `.popitem()` | `d.popitem()`   | Remove last pair      |
| `.clear()`   | `d.clear()`     | Empty dictionary      |
| `.copy()`    | `d.copy()`      | Copy dictionary       |

- Code:

```python
# Dictionaries are mutable data structures in Python.
# They are unordered collections of key-value pairs.
# Keys must be unique and immutable (strings, numbers, tuples), while values can be mutable or immutable.

# Examples of dictionaries
my_dict = {'key': 'value'}
student = {'name': 'Alice', 'age': 20, 'grade': 'A'}
book = {'title': 'Python Cookbook', 'author': 'David Beazley'}
empty_dict = {}

# Nested dictionary
nested_dict = {'person': {'name': 'John', 'age': 25}, 'address': {'city': 'New York', 'zipcode': 10001}}

# Accessing elements in a dictionary
print(student['name'])  # Output: Alice

# Adding or updating elements in a dictionary
student['grade'] = 'B'  # Updating existing value
student['gender'] = 'Female'  # Adding new key-value pair

# Removing elements from a dictionary
del student['age']  # Removes the 'age' key-value pair
removed_value = student.pop('grade')  # Removes and returns the value associated with 'grade' key

# Dictionary comprehension
squares = {x: x**2 for x in range(1, 6)}  # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Getting keys and values
keys = student.keys()  # Returns a view object containing the keys
values = student.values()  # Returns a view object containing the values
items = student.items() # Returns items in a list format of key/value tuple pairs 

# Iterating over keys and values
for key, value in student.items():
    print(key, value)

# Check if a key exists in a dictionary
if 'age' in student:
    print("Age exists in the dictionary")

# Clearing a dictionary
student.clear()  # Removes all items from the dictionary

# clear(): Removes all items from the dictionary.
my_dict = {'a': 1, 'b': 2, 'c': 3}
my_dict.clear()
# print(my_dict)  # Output: {}

# copy(): Returns a shallow copy of the dictionary.
original_dict = {'a': 1, 'b': 2, 'c': 3}
copied_dict = original_dict.copy()
# print(copied_dict)  # Output: {'a': 1, 'b': 2, 'c': 3}

# fromkeys(seq[, value]): Returns a new dictionary with keys from sequence and values set to value (default is None).
new_dict = dict.fromkeys(['a', 'b', 'c'], 0)
# print(new_dict)  # Output: {'a': 0, 'b': 0, 'c': 0}

# get(key[, default]): Returns the value for key if key is in the dictionary, else default (None by default).
my_dict = {'a': 1, 'b': 2, 'c': 3}
value = my_dict.get('d', 0)
# print(value)  # Output: 0

# items(): Returns a new view of the dictionary's items (key-value pairs).
my_dict = {'a': 1, 'b': 2, 'c': 3}
items = my_dict.items()
# print(items)  # Output: dict_items([('a', 1), ('b', 2), ('c', 3)])

# keys(): Returns a new view of the dictionary's keys.
my_dict = {'a': 1, 'b': 2, 'c': 3}
keys = my_dict.keys()
# print(keys)  # Output: dict_keys(['a', 'b', 'c'])

# pop(key[, default]): Removes and returns the value associated with key.
my_dict = {'a': 1, 'b': 2, 'c': 3}
value = my_dict.pop('b')
# print(value)  # Output: 2

# popitem(): Removes and returns an arbitrary (key, value) pair from the dictionary.
my_dict = {'a': 1, 'b': 2, 'c': 3}
item = my_dict.popitem()
# print(item)  # Output: ('c', 3)

# setdefault(key[, default]): Returns the value for key if key is in the dictionary, 
# else default is inserted into the dictionary and returned.
my_dict = {'a': 1, 'b': 2, 'c': 3}
value = my_dict.setdefault('d', 0)
# print(value)  # Output: 0

# update([other]): Updates the dictionary with the key-value pairs from other.
my_dict = {'a': 1, 'b': 2}
my_dict.update({'b': 3, 'c': 4})
# print(my_dict)  # Output: {'a': 1, 'b': 3, 'c': 4}

# values(): Returns a new view of the dictionary's values.
my_dict = {'a': 1, 'b': 2, 'c': 3}
values = my_dict.values()
# print(values)  # Output: dict_values([1, 2, 3])
```

---

## 🧩 Set types

| Type        | Example                | Description                           |
| ----------- | ---------------------- | ------------------------------------- |
| `set`       | `{1, 2, 3}`            | Unordered collection of unique values |
| `frozenset` | `frozenset({1, 2, 3})` | Immutable set                         |

- Code:

| Method            | Example             | Purpose                  |
| ----------------- | ------------------- | ------------------------ |
| `.add()`          | `s.add(5)`          | Add item                 |
| `.remove()`       | `s.remove(5)`       | Remove item              |
| `.discard()`      | `s.discard(5)`      | Remove without error     |
| `.pop()`          | `s.pop()`           | Remove an arbitrary item |
| `.union()`        | `a.union(b)`        | Combine sets             |
| `.intersection()` | `a.intersection(b)` | Common values            |
| `.difference()`   | `a.difference(b)`   | Values only in first set |
| `.issubset()`     | `a.issubset(b)`     | Check subset             |

- Code:

```python
# Sets contain unique elements
# Items can be of different data types (integer, floats, strings, etc.)
# Sets are mutable but cannot contain mutable elements like lists

# Examples of sets
unique_numbers = {1, 2, 3, 4, 5}
float_set = {2.5, 3.7, 1.0}
empty_set = set()

# Empty Set:
empty_set = set()

# Set with Elements:
numbers_set = {1, 2, 3, 4, 5}
fruits_set = {'apple', 'banana', 'orange'}
mixed_set = {1, 'apple', True, (2, 'banana'), None}

# Nested Sets (Sets of Sets):
# Sets cannot contain mutable elements, so nested sets are not common.
# However, you can have sets of frozensets, which are immutable.
matrix_set = {frozenset({1, 2, 3}), frozenset({4, 5, 6}), frozenset({7, 8, 9})}

# Set Comprehension:
# Similar to list comprehensions, sets also support comprehension.
# You can create a set from a generator expression using curly braces {}:
squares_set = {x**2 for x in range(1, 6)}  # {1, 4, 9, 16, 25}

# Using set() Function:
# You can create a set from an iterable using the set() function:
numbers_set = set(range(1, 6))  # {1, 2, 3, 4, 5}

# Creating a Copy of a Set:
original_set = {1, 2, 3}
copied_set = original_set.copy()  # Using copy() method to create a copy

# Set is mutable
unique_numbers.add(6)  # Result: {1, 2, 3, 4, 5, 6}

# Set operations: union, intersection, difference, symmetric difference
set1 = {1, 2, 3, 4, 5}
set2 = {4, 5, 6, 7, 8}
union_set = set1 | set2  # Union: {1, 2, 3, 4, 5, 6, 7, 8}
intersection_set = set1 & set2  # Intersection: {4, 5}
difference_set1 = set1 - set2  # Difference: {1, 2, 3}
difference_set2 = set2 - set1 # Difference: {8, 6, 7}
symmetric_difference_set = set1 ^ set2  # Symmetric Difference: {1, 2, 3, 6, 7, 8}

# Set methods: add, remove, discard, pop, clear
unique_numbers.add(7)  # Adds 7 to the set
unique_numbers.remove(5)  # Removes 5 from the set
unique_numbers.discard(8)  # Discards 8 from the set (if present)
popped_element = unique_numbers.pop()  # Removes and returns an arbitrary element from the set
unique_numbers.clear()  # Removes all elements from the set

# Set comprehension
squares_set = {x**2 for x in range(10)}  # {0, 1, 4, 9, 16, 25, 36, 49, 64, 81}

# len(set):
# Returns the number of elements in the set.
unique_numbers = {1, 2, 3, 4, 5}
length = len(unique_numbers)  # Result: 5

# max(set):
# Returns the maximum value in the set.
numbers = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5}
max_value = max(numbers)  # Result: 9

# min(set):
# Returns the minimum value in the set.
numbers = {3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5}
min_value = min(numbers)  # Result: 1

# sum(set):
# Returns the sum of all elements in the set.
numbers = {1, 2, 3, 4, 5}
total = sum(numbers)  # Result: 15


#################################################################
# Sets are mutable but cannot contain mutable elements like lists
# Creating a set with immutable elements
immutable_set = {1, 2, 3}

# Attempting to add a list to the set
try:
    immutable_set.add([4, 5, 6])  # Trying to add a list to the set
except TypeError as e:
    print("TypeError:", e)  # TypeError: unhashable type: 'list'

# Creating a set with mutable elements
mutable_set = {['a', 'b'], ('c', 'd')}  # Tuple is immutable, list is mutable

# Attempting to add a list to the set
try:
    mutable_set.add(['e', 'f'])  # Trying to add a list to the set
except TypeError as e:
    print("TypeError:", e)  # TypeError: unhashable type: 'list'

# Modifying an existing mutable element in the set
mutable_set.remove(['a', 'b'])  # Removing the list ['a', 'b']
mutable_set.add(['g', 'h'])  # Adding a new list ['g', 'h']
# print("Updated set:", mutable_set) # will result in TypeError: unhashable type: 'list'

# However, it can contain tuples
# Creating a set with mutable elements
my_set = {('a', 'b'), ('c', 'd')}  # Use tuples instead of lists

# Modifying an existing mutable element in the set
my_set.remove(('a', 'b'))  # Removing the tuple ('a', 'b')
my_set.add(('g', 'h'))  # Adding a new tuple ('g', 'h')
# print("Updated set:", my_set) 
# {('c', 'd'), ('g', 'h')}
```

---

## 💾 Binary types

| Type         | Example                | Description           |
| ------------ | ---------------------- | --------------------- |
| `bytes`      | `b"Hello"`             | Immutable binary data |
| `bytearray`  | `bytearray(5)`         | Mutable binary data   |
| `memoryview` | `memoryview(b"Hello")` | View of binary data   |

---

## 🚫 None type

| Type       | Example | Description                       |
| ---------- | ------- | --------------------------------- |
| `NoneType` | `None`  | Represents the absence of a value |

---

