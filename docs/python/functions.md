# Functions

## Overview

- Function Definition: Functions in Python are defined using the def keyword, followed by the function name and parentheses `( )`. Optionally, `parameters (arguments)` may be specified within the parentheses.

- Function Invocation: Functions are invoked (called) using their name followed by parentheses `( )`. If the function requires arguments, they are passed within the parentheses.

- Return Statement: Functions can return values using the return statement. This statement terminates the function and optionally specifies the value to be returned to the caller.

- Default Arguments: Function parameters can have default values, which are used when the function is called without specifying a value for that parameter.

- Variable Scope: Variables defined inside a function have local scope by default, meaning they can only be accessed within the function. Variables defined outside any function have global scope and can be accessed from anywhere in the code.

- Global Keyword: The global keyword is used to declare that a variable inside a function has global scope, allowing it to be modified from within the function.

- Lambda Functions: Lambda functions (also known as anonymous functions) are small, single-expression functions defined using the lambda keyword. They are often used for short, simple operations.

- Docstrings: Docstrings are multi-line strings placed immediately after the function definition, which serve as documentation for the function. They are accessible via the __doc__ attribute of the function.

- Function Annotations: Function annotations are metadata attached to function parameters and return values, indicating the expected types or any other relevant information. They are purely informational and do not affect the function's behavior.

- Recursion: Recursion is a programming technique where a function calls itself in order to solve a problem. It is particularly useful for solving problems that can be broken down into smaller, similar subproblems.

- Function Overloading: Python does not support function overloading in the same way as some other languages like C++ or Java. However, you can achieve similar behavior using default arguments or variable-length argument lists.

- Error Handling: Functions can raise exceptions using the raise statement to indicate errors or exceptional conditions. You can catch and handle exceptions using try, except, finally and else blocks.nally, and else blocks.

## Functions

```python
# def functionName(parameters):
#     """docstring"""
#     statement(s)

# Example:
def greet(name):
    """This function greets the person passed in as parameter."""
    print("Hello, " + name + ". Good morning!")

# greet('John')  # Output: Hello, John. Good morning!

# Parameters:
# Functions can take parameters, which are values you supply to the function so it can process them.
# Parameters are specified within the parentheses.
# You can add as many parameters as you want, just separate them with a comma.

# Return Statement:
# The return statement is used to exit a function and optionally pass back a value.

# Example:
def add(a, b):
    """This function adds two numbers."""
    return a + b

result = add(5, 3)
# print(result)  # Output: 8

# Scope of Variables:
# Variables defined inside a function are not accessible from outside the function.

# Example:
def myfunc():
    x = 300
    print(x)

# myfunc()  # Output: 300

# Global Variables:
# Variables declared outside a function are global variables and can be accessed throughout the program.

# Example:
x = 300

def myfunc():
    print(x)

# myfunc()  # Output: 300

# Docstrings:
# Python documentation strings (docstrings) provide a convenient way of associating documentation with functions.

# Example:
def greet(name):
    """This function greets the person passed in as parameter."""
    print("Hello, " + name + ". Good morning!")

# print(greet.__doc__)  # Output: This function greets the person passed in as parameter.


# Nested functions

def outer_function():
    """This is the outer function."""
    
    def inner_function():
        """This is the inner function."""
        print("This is the inner function.")
    
    print("This is the outer function.")
    inner_function()  # Calling the inner function inside the outer function

# Calling the outer function
# outer_function()

# Output:
# This is the outer function.
# This is the inner function.
```

### args and kwargs

```python
# *args and **kwargs in Python

# *args:
# *args is used to pass a variable number of non-keyworded arguments to a function.
# It allows you to pass any number of arguments to a function.

# Example:
def my_function(*args):
    for arg in args:
        print(arg)

# my_function(1, 2, 3, 4, 5)
# Output:
# 1
# 2
# 3
# 4
# 5

# **kwargs:
# **kwargs allows you to pass a variable number of keyworded arguments to a function.
# It allows you to handle named arguments in your function.

# Example:
def my_function(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

# my_function(name="John", age=30, city="New York")
# Output:
# name: John
# age: 30
# city: New York

# You can use both *args and **kwargs in the same function, but *args must appear before **kwargs.

# Example:
def my_function(arg1, arg2, *args, **kwargs):
    print("First normal argument:", arg1)
    print("Second normal argument:", arg2)
    print("Additional non-keyworded arguments (*args):", args)
    print("Additional keyworded arguments (**kwargs):", kwargs)

# my_function("Hello", "World", 1, 2, 3, name="John", age=30)
# Output:
# First normal argument: Hello
# Second normal argument: World
# Additional non-keyworded arguments (*args): (1, 2, 3)
# Additional keyworded arguments (**kwargs): {'name': 'John', 'age': 30}
```

---

## Lambda Functions

```python
# Lambda Functions:
# Lambda functions, also known as anonymous functions, are small, single-expression functions.
# They are defined using the lambda keyword, followed by the parameters and a colon, 
# and then the expression to be evaluated.
# Lambda functions can take any number of arguments, but they can only have one expression.

# Example:
add = lambda x, y: x + y
print(add(3, 5))  # Output: 8

# Parameters:
# Lambda functions can take any number of parameters, separated by commas.
# Parameters are specified before the colon in the lambda function definition.

# Return Value:
# Lambda functions automatically return the result of evaluating the expression.
# There's no need to use the return statement explicitly.

# Example:
# Multiple two numbers
multiply = lambda x, y: x * y
print(multiply(3, 5))  # Output: 15

# Use Cases:
# Lambda functions are often used as arguments to higher-order functions such as map(), filter(), and sort().
# They are also useful for defining simple functions inline, 
# especially when the function is short and used only once.

# Example:
numbers = [1, 2, 3, 4, 5]
squared_numbers = list(map(lambda x: x ** 2, numbers))
# print(squared_numbers)  # Output: [1, 4, 9, 16, 25]

# Scope:
# Lambda functions have their own local scope and cannot access variables from the enclosing scope
# unless explicitly passed as arguments.

# Example:
x = 10
y = 20
add_with_x = lambda z: x + z
# print(add_with_x(y))  # Output: 30

# Note: In the above example, the lambda function can access the variable x from the enclosing scope.

# Limitations:
# Lambda functions are limited to a single expression, which means they cannot contain multiple statements 
# or complex logic. For more complex functions, it's better to use regular named functions.

# Example:
# This lambda function calculates the square of a number.
square = lambda x: x ** 2
# print(square(5))  # Output: 25

# Docstrings:
# Lambda functions can have docstrings just like regular functions, but they are less common since lambda functions
# are typically short and self-explanatory.

# Example:
power = lambda x, n: x ** n
# print(power.__doc__)  # Output: None (lambda functions do not have a __doc__ attribute by default)
```

### String

```python
# Reversing a string
reverse_string = lambda s: s[::-1]
# print(reverse_string("hello"))  # Output: "olleh"

# Capitalizing the first letter of a string
capitalize_first_letter = lambda s: s.capitalize()
# print(capitalize_first_letter("python"))  # Output: "Python"
```

### Numbers

```python
# Adding two numbers
add = lambda x, y: x + y
# print(add(5, 3))  # Output: 8

# Squaring a number
square = lambda x: x ** 2
# print(square(4))  # Output: 16
```

---

### Dictionaries

```python
# Doubling each element of a list
double_list = lambda lst: [x * 2 for x in lst]
# print(double_list([1, 2, 3]))  # Output: [2, 4, 6]

# Sorting a list of tuples based on the second element
students = [("Alice", 23), ("Bob", 20), ("Charlie", 21)]
sorted_students = sorted(students, key=lambda x: x[1])
# print(sorted_students)  # Output: [("Bob", 20), ("Charlie", 21), ("Alice", 23)]
```

---