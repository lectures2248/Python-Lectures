# Object-Oriented Programming (OOP) in Python

Object-Oriented Programming (OOP) is a way of writing programs that
focuses on **objects**---real-world entities---and organizes code around
them.

> **In Python, everything is an object!**

------------------------------------------------------------------------

# Why OOP?

OOP helps make code:

-   Easier to understand
-   Easier to reuse
-   Easier to maintain
-   More organized

Let's understand OOP step by step.

------------------------------------------------------------------------

# What is an Object?

An object is something that has:

## Attributes (Data)

Example: - Color - Model - Speed

## Behavior (Methods)

Example: - `start()` - `stop()` - `accelerate()`

### Real-Life Example

A **Car** has:

-   Attributes: `color`, `model`, `speed`
-   Behavior: `start()`, `stop()`, `accelerate()`

------------------------------------------------------------------------

# What is a Class?

A class is a **blueprint** or **template** for creating objects.

### Example

``` python
class Car:
    # attributes and methods will go here
```

Once a class is defined, we can create multiple objects from it.

------------------------------------------------------------------------

# Creating a Simple Class and Object

``` python
class Car:

    brand = ""
    model = ""
    color = ""
    year = 0
    price = 0.0

    def show_details(self):
        print("Brand:", self.brand)
        print("Model:", self.model)
```

------------------------------------------------------------------------

# Constructor in Python

A constructor initializes an object automatically when it is created.

## Syntax

``` python
def __init__(self, parameters):
    # initialize attributes
```

## Example

``` python
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age

s1 = Student("Ali",20)

print(s1.name)
print(s1.age)
```

------------------------------------------------------------------------

# Instance Variables vs Class Variables

## Instance Variables

-   Belong to each object separately
-   Defined inside the constructor using `self`

## Class Variables

-   Shared among all objects
-   Defined outside the constructor but inside the class

``` python
class Student:

    school_name = "ABC High School"

    def __init__(self,name,age):
        self.name = name
        self.age = age

s1 = Student("Ali",18)
s2 = Student("Sara",19)

print(s1.name,"-",s1.school_name)
print(s2.name,"-",s2.school_name)
```

------------------------------------------------------------------------

# Methods in Classes

There are **3 types of methods**:

1.  Instance Methods
2.  Class Methods
3.  Static Methods

``` python
class MathOperations:

    count = 0

    def __init__(self,value):
        self.value = value

    def square(self):
        return self.value * self.value

    @classmethod
    def show_count(cls):
        print("Class variable count =", cls.count)

    @staticmethod
    def greet():
        print("Hello from MathOperations!")

obj = MathOperations(4)

print(obj.square())
MathOperations.greet()
MathOperations.show_count()
```

------------------------------------------------------------------------

# Encapsulation (Data Hiding)

-   `_variable` → Protected
-   `__variable` → Private

``` python
class BankAccount:

    def __init__(self,balance):
        self.__balance = balance

    def deposit(self,amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance
```

Private variables can be accessed using name mangling:

``` python
acc._BankAccount__balance
```

------------------------------------------------------------------------

# Inheritance

A child class inherits attributes and methods from a parent class.

``` python
class Animal:

    def sound(self):
        print("Animals make sounds")

class Dog(Animal):

    def bark(self):
        print("Dog barks")
```

## Types of Inheritance

-   Single
-   Multiple
-   Multilevel

------------------------------------------------------------------------

# Polymorphism

Polymorphism means **many forms**.

``` python
class Parent:
    def show(self):
        print("Parent method")

class Child(Parent):
    def show(self):
        print("Child method")
```

------------------------------------------------------------------------

# Abstraction

Abstraction hides implementation details and shows only essential
features.

``` python
from abc import ABC, abstractmethod

class Shape(ABC):

    @abstractmethod
    def area(self):
        pass

class Circle(Shape):

    def __init__(self,radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius * self.radius
```

Output

``` text
Area: 78.5
```

------------------------------------------------------------------------

# Practical Task

## Employee Management System

Create a class **Employee** that stores:

-   Name
-   Position
-   Salary

### Include

-   Constructor
-   Display method
-   Bonus method

### Hint

``` python
class Employee:

    def __init__(self,name,position,salary):
        self.name = name
        self.position = position
        self.salary = salary

    def give_bonus(self,amount):
        self.salary += amount

    def show(self):
        print("Name:",self.name)
        print("Position:",self.position)
        print("Salary:",self.salary)

e1 = Employee("Ali","Manager",50000)
e2 = Employee("Sara","Clerk",30000)

e1.give_bonus(5000)
e2.give_bonus(2000)

e1.show()
e2.show()
```
