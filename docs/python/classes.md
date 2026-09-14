# Classes

## Class Basics

 - Class: A blueprint for creating objects. It defines properties and behaviors that objects of the class will have.

```python 
class MyClass:
    pass

# Object: An instance of a class.
obj = MyClass()
```

- Attributes: Variables associated with a class or its objects.

```python
class Dog:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

- Methods: Functions defined within a class.

```python
class Dog:
    def bark(self):
        print("Woof!")
```

- Constructor (__init__): Special method used for initializing objects.

```python
class Dog:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

- Inheritance: The mechanism where a new class inherits properties and methods from a parent class.

```python
class Animal:
    def sound(self):
        pass

class Dog(Animal):
    def sound(self):
        print("Woof!")
```

- Encapsulation: The bundling of data (attributes) and methods that operate on the data into a single unit (class).

```python
class BankAccount:
    def __init__(self):
        self.balance = 0

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        self.balance -= amount
```

- Polymorphism: The ability of different objects to respond to the same message or method call in different ways.

- Animal (superclass), cat & dog (subclasses), subclasses inherit attributes and methods from their superclass.

```python
class Animal:
    def sound(self):
        pass

class Dog(Animal):
    def sound(self):
        print("Woof!")

class Cat(Animal):
    def sound(self):
        print("Meow!")

# Class Variables: Variables that are shared among all instances of a class.
class Dog:
    species = 'mammal'

```

- Static Methods: Methods that are bound to a class rather than its object.

```python
class Math:
    @staticmethod
    def add(a, b):
        return a + b
```

-  Class Methods: Methods that are bound to the class and can access or modify class-level attributes.

```python
class MyClass:
    count = 0

    @classmethod
    def increase_count(cls):
        cls.count += 1
```

---

## Class Notes

### __init__()

The first thing to note is that a function within a class is known as a method. A particular type of method that runs when an instance of the class is created is an initializer. The __init__ method is known as a dunder, double-underscore or magic method, and these tend to be used on classes mainly. They use double underscores so as not to conflict with your own defined classes.

An __init__() method on its own would simply create an empty class object. However, an __init__() method can take arguments. One of the advantages of object-orientated programming is the ability to model the real world in code. If you were writing software to use in a car factory or dealership, you could use a class to create an object Car that has the same properties and attributes as a real ca

__init__(self, ...): The __init__ method is called automatically when a new instance of a class is created. It is used to initialize the object's attributes. Any parameters specified in the __init__ method are used to pass initial values to the object's attributes. The first parameter of __init__ is always self, which refers to the instance being created.r.

---

### `self` keyword
The self keyword associates functions and properties with a class. It also holds references to data and behaviour of particular instances of a class. It’s customary to use self to refer to the class instance, but in fact, any variable could be used. It must be the first parameter of any function in the class. Python simply uses self to state to what instance to assign an instance attribute.

The self parameter is a reference to the current instance of the class. It is used to access variables and methods within the class. When a method is called on an instance (obj.method()), Python automatically passes the instance as the first argument to the method, which is why self is used as the first parameter in class methods. By convention, self is the name used for this parameter, but you can technically use any name, although it's highly recommended to stick with self for clarity and consistency.

---

### Example to illustrate how init() and self work together:

```python
class Car:
    def __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year
    
    def get_info(self):
        return f"{self.year} {self.make} {self.model}"

# Creating an instance of Car
my_car = Car("Toyota", "Camry", 2020)

# Accessing attributes using self
print(my_car.get_info())  # Output: 2020 Toyota Camry
```

In this example, `__init__()` initializes the make, model, and year attributes of the Car object. Inside the get_info() method, self is used to access these attributes and return a formatted string representing the car's information. When we create an instance of Car, we pass arguments to `__init__()` (e.g., "Toyota", "Camry", 2020), and self refers to this instance throughout the class methods.

---

## Class Examples

### BankAccount example

Python classes are used in real-life scenarios to model objects or entities with properties and behaviors. Let's consider a real-life example of modeling a BankAccount class:

```python
class BankAccount:
    def __init__(self, account_number, owner_name, balance=0):
        self.account_number = account_number
        self.owner_name = owner_name
        self.balance = balance

    def deposit(self, amount):
        """Deposit money into the account."""
        self.balance += amount
        print(f"Deposited {amount}. Current balance: {self.balance}")

    def withdraw(self, amount):
        """Withdraw money from the account if sufficient balance."""
        if self.balance >= amount:
            self.balance -= amount
            print(f"Withdrew {amount}. Current balance: {self.balance}")
        else:
            print("Insufficient funds.")

    def display_balance(self):
        """Display the current balance."""
        print(f"Account balance for {self.owner_name}: {self.balance}")


# Create instances of the BankAccount class
account1 = BankAccount("123456789", "John Doe")
account2 = BankAccount("987654321", "Jane Smith", 1000)

# Perform operations on the accounts
account1.deposit(500)             # Output: Deposited $500. Current balance: $500
account2.withdraw(200)            # Output: Withdrew $200. Current balance: $800
account1.display_balance()        # Output: Account balance for John Doe: $500
account2.display_balance()        # Output: Account balance for Jane Smith: $800
```

In this example, the BankAccount class models a real-life bank account. Each instance of the class represents a specific bank account with properties like account_number, owner_name, and balance. The class also provides methods such as deposit, withdraw, and display_balance to perform operations on the account, such as depositing money, withdrawing money, and displaying the account balance. This BankAccount class demonstrates how Python classes can be used to model real-life entities with properties and behaviors.

### Car Example

```python
class Car:
    def __init__(self, make, model, year, color):
        self.make = make
        self.model = model
        self.year = year
        self.color = color
        self.odometer_reading = 0  # Default value for odometer reading
    
    def get_descriptive_name(self):
        """Return a descriptive name of the car."""
        return f"{self.year} {self.make} {self.model} in {self.color}"
    
    def read_odometer(self):
        """Print the car's mileage."""
        print(f"This car has {self.odometer_reading} miles on it.")
    
    def update_odometer(self, mileage):
        """
        Set the odometer reading to the given value.
        Reject the change if it attempts to roll back the mileage.
        """
        if mileage >= self.odometer_reading:
            self.odometer_reading = mileage
        else:
            print("You can't roll back an odometer!")
    
    def increment_odometer(self, miles):
        """Add the given amount to the odometer reading."""
        if miles >= 0:
            self.odometer_reading += miles
        else:
            print("You can't decrease the odometer reading!")

# Example usage
my_car = Car("Toyota", "Camry", 2022, "blue")
print(my_car.get_descriptive_name())  # Output: 2022 Toyota Camry in blue

my_car.read_odometer()  # Output: This car has 0 miles on it.

# Update the odometer reading
my_car.update_odometer(15000)
my_car.read_odometer()  # Output: This car has 15000 miles on it.

# Increment the odometer reading
my_car.increment_odometer(100)
my_car.read_odometer()  # Output: This car has 15100 miles on it.
```

In this example, the Car class represents a car with attributes such as make, model, year, color, and odometer reading. It also provides methods to get a descriptive name of the car, read the odometer, update the odometer reading, and increment the odometer reading. This class allows you to create car objects and perform operations related to the car's attributes.

### Rectangle Example

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        """Calculate the area of the rectangle."""
        return self.width * self.height
    
    def perimeter(self):
        """Calculate the perimeter of the rectangle."""
        return 2 * (self.width + self.height)

# Example usage
my_rectangle = Rectangle(5, 3)
print("Width:", my_rectangle.width)       # Output: Width: 5
print("Height:", my_rectangle.height)     # Output: Height: 3
print("Area:", my_rectangle.area())      # Output: Area: 15
print("Perimeter:", my_rectangle.perimeter())  # Output: Perimeter: 16
```

In this example, the Rectangle class represents rectangles with attributes width and height. It provides methods to calculate the area and perimeter of the rectangle. This class allows you to create rectangle objects and perform calculations related to their dimensions.


---

## Superclass & Subclass Example

### Vehicle Example

```python
class Vehicle:
    def __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year
    
    def start(self):
        """Start the vehicle."""
        print(f"{self.year} {self.make} {self.model} started.")

    def stop(self):
        """Stop the vehicle."""
        print(f"{self.year} {self.make} {self.model} stopped.")

class Car(Vehicle):
    def __init__(self, make, model, year, num_doors):
        super().__init__(make, model, year)
        self.num_doors = num_doors

    def drive(self):
        """Drive the car."""
        print(f"{self.year} {self.make} {self.model} with {self.num_doors} doors is driving.")

# Example usage
car1 = Car("Toyota", "Camry", 2020, 4)
car1.start()  # Output: 2020 Toyota Camry started.
car1.drive()  # Output: 2020 Toyota Camry with 4 doors is driving.
car1.stop()   # Output: 2020 Toyota Camry stopped.
In this example, the Vehicle class defines common attributes and methods shared by all vehicles, such as make, model, year, start, and stop. The Car class inherits from Vehicle and adds a specific attribute num_doors and method drive for cars.

We create an instance of the Car class (car1) and call its methods to start, drive, and stop the car, demonstrating the inheritance and function calls in action.

6.4.2. Bird Example
class Bird:
    """
    Bird class
    """
    # class attribute
    definition = "a warm-blooded egg-laying vertebrate animal distinguished by the possession of feathers, wings, a beak, and typically by being able to fly."

    def __init__(self, kind, call):
        # instance attribute
        self.kind = kind
        self.call = call

    def description(self):
        """
        describe the bird
        """
        parrot = "Norwegian Blue"
        return f"A {self.kind} goes {self.call} and is {self.definition} It is not a {parrot}" 
       

owl = Bird('owl', 'Twit Twoo!')
print(owl.description())
```

---

## Class-Related Decorators

1. `@classmethod`:

This decorator is used to define a method that operates on the class itself rather than on instances of the class. It receives the class (not the instance) as its first argument conventionally named cls. It can be applied to a method using @classmethod decorator.

```python
class MyClass:
    class_variable = "Class variable"

    @classmethod
    def class_method(cls):
        print("Accessing class variable:", cls.class_variable)

# Calling the class method
MyClass.class_method()
# Output: Accessing class variable: Class variable
```

---

2. `@staticmethod`:
This decorator is used to define a static method within a class. Unlike regular methods, static methods do not receive the instance or class as the first argument. They are primarily used for utility functions that do not require access to instance or class attributes.

```python
class MyClass:
    @staticmethod
    def static_method():
        print("This is a static method")

# Calling the static method
MyClass.static_method()
Output: This is a static method
```

---

3. `@property`:
This decorator is used to define properties in a class. Properties allow defining getter, setter, and deleter methods in a way that they can be accessed and assigned like attributes, without explicit method calls. It's commonly used to create computed attributes or to encapsulate the access to private attributes.

```python
class MyClass:
    def __init__(self):
        self._x = None

    @property
    def x(self):
        return self._x

    @x.setter
    def x(self, value):
        if value < 0:
            raise ValueError("Value must be non-negative")
        self._x = value

# Creating an instance of MyClass
obj = MyClass()

# Accessing property 'x'
print(obj.x)  # Output: None

# Setting property 'x'
obj.x = 10

# Accessing property 'x' again
print(obj.x)  # Output: 10

# Trying to set a negative value
obj.x = -5  # Raises ValueError
```

---

## getter, setter, deleter @property

The purpose of getter, setter, and deleter methods in the @property decorator in Python is to provide controlled access to class attributes, allowing for validation, encapsulation, and managing attribute behavior.

**Getter Method**: It allows you to define a method to retrieve the value of an attribute. By using `@property`, you can access the method like an attribute without directly calling it as a method. This is useful for calculating or transforming attribute values on-the-fly.

**Setter Method**: It allows you to define a method to set the value of an attribute. By using `@<property_name>.setter`, you can define custom behavior when setting the attribute value, such as validation checks or triggering actions based on the new value.

**Deleter Method**: It allows you to define a method to delete an attribute. By using @<property_name>.deleter, you can define custom behavior to be executed when the attribute is deleted, such as releasing resources or performing cleanup tasks.

> These methods help ensure that the class attributes are accessed, modified, and deleted in a controlled manner, enforcing data integrity and encapsulation principles. They also provide a convenient way to implement properties in Python classes, making the code more readable and maintainable.

```python
class Rectangle:
    def __init__(self, width, height):
        self._width = width
        self._height = height

    @property
    def width(self):
        return self._width

    @width.setter
    def width(self, value):
        if value <= 0:
            raise ValueError("Width must be positive")
        self._width = value

    @property
    def height(self):
        return self._height

    @height.setter
    def height(self, value):
        if value <= 0:
            raise ValueError("Height must be positive")
        self._height = value

    @property
    def area(self):
        return self._width * self._height

    @property
    def perimeter(self):
        return 2 * (self._width + self._height)

    @property
    def diagonal(self):
        return (self._width ** 2 + self._height ** 2) ** 0.5

    @width.deleter
    def width(self):
        del self._width

    @height.deleter
    def height(self):
        del self._height


# Creating an instance of Rectangle
rect = Rectangle(5, 10)

# Accessing properties
print("Width:", rect.width)
print("Height:", rect.height)
print("Area:", rect.area)
print("Perimeter:", rect.perimeter)
print("Diagonal:", rect.diagonal)

# Setting new values using setters
rect.width = 8
rect.height = 12

# Accessing properties again after setting new values
print("Updated Width:", rect.width)
print("Updated Height:", rect.height)
print("Updated Area:", rect.area)
print("Updated Perimeter:", rect.perimeter)
print("Updated Diagonal:", rect.diagonal)

# Deleting properties
del rect.width
# Trying to access the width property after deleting
print("Width after deletion:", rect.width)  # Raises AttributeError
```

---

## Mixins

In Python, mixins are a way to provide additional functionality to classes by "mixing in" methods and attributes from other classes without using inheritance in the traditional sense. Mixins are typically small, focused classes that provide a specific set of behaviors or functionalities.

The key characteristics of mixins include:

- **Modularity**: Mixins encapsulate specific functionality, making it easy to add or remove features from a class by including or excluding the corresponding mixin.
- **Reusability**: Mixins can be reused across multiple classes, promoting code reuse and reducing duplication.
- **Composition over Inheritance**: Instead of relying solely on class inheritance, mixins allow classes to compose functionality by including mixins as part of their definition, providing more flexibility and avoiding some of the limitations of multiple inheritance.
- **Single Responsibility Principle**: Each mixin typically encapsulates a single behavior or functionality, adhering to the principle of single responsibility and promoting cleaner, more maintainable code.
- **Avoidance of Diamond Problem**: Unlike multiple inheritance, mixins help avoid the "diamond problem," a situation where ambiguity arises when a class inherits from multiple classes with conflicting methods or attributes.

* Convention: Mixins are often named with a Mixin suffix to indicate their purpose and usage.

```python
class JSONMixin:
    def to_json(self):
        import json
        return json.dumps(self.__dict__)

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

class Employee(Person, JSONMixin):
    def __init__(self, name, age, position):
        super().__init__(name, age)
        self.position = position
```

- Example usage:

```python
employee = Employee("Alice", 30, "Software Engineer")
print(employee.to_json())  
# Output: {"name": "Alice", "age": 30, "position": "Software Engineer"}
```

In this example, JSONMixin provides the to_json() method, which converts an object to its JSON representation. By including JSONMixin in the definition of the Employee class, instances of Employee gain the ability to serialize themselves to JSON format, without directly inheriting from a JSON serialization class.