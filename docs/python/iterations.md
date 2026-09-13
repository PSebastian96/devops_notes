# Iterations

## Overview

Loops (Iteration) let you repeat code without writing it multiple times. The two main loops in Python are:

>`for` — repeat over a collection or a known sequence

> `while` — repeat while a condition remains True

!!! warning "Be careful with infinite loops"

    This is a problem:

    ```python
    count = 0

    while count < 5:
        print(count)
    ```

    - count never changes, so the condition will always be True.

    - You need:

    ```python
    count += 1
    ```

## Example
```python
# Loops/Iterations in Python

# for loop:
# The for loop is used to iterate over a sequence (e.g., list, tuple, string) or other iterable objects.
# It iterates over each item in the sequence and executes the block of code for each item.

# Example:
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
   # print(fruit)

# Output:
# apple
# banana
# cherry

# range() function:
# The range() function generates a sequence of numbers that can be used to iterate over a block of code 
# a specified number of times.

# Example:
for i in range(5):
   # print(i)

# Output:
# 0
# 1
# 2
# 3
# 4

# while loop:
# The while loop is used to repeatedly execute a block of code as long as a specified condition is True.
# It continues iterating until the condition becomes False.

# Example:
i = 1
while i <= 5:
  #  print(i)
    i += 1

# Output:
# 1
# 2
# 3
# 4
# 5

# Nested loops:
# You can nest one or more loops inside another loop to perform more complex iterations.

# Example:
adj = ["red", "big", "tasty"]
fruits = ["apple", "banana", "cherry"]

for a in adj:
    for f in fruits:
       # print(a, f)

# Output:
# red apple
# red banana
# red cherry
# big apple
# big banana
# big cherry
# tasty apple
# tasty banana
# tasty cherry

# Loop Control Statements:
# Python provides loop control statements like break, continue, and pass to control the flow of loops.

# Example with break:
for i in range(10):
    if i == 3:
        break
   # print(i)

# Output:
# 0
# 1
# 2

# Example with continue:
for i in range(5):
    if i == 2:
        continue
   # print(i)

# Output:
# 0
# 1
# 3
# 4

# Example with pass:
for i in range(5):
    if i == 2:
        pass
  #  print(i)

# Output:
# 0
# 1
# 2
# 3
# 4
```