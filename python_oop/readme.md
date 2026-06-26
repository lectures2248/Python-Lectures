Object-Oriented Programming (OOP) in Python

Object-Oriented Programming (OOP) is a way of writing programs that focuses on \*objects\*

— real-world entities — and organizes code around them.

In Python, everything is an object!

\-------------------------------------------------

OOP helps make code:

\- Easier to understand

\- Easier to reuse

\- Easier to maintain

\- More organized

Let's understand OOP step by step.

What is an Object?

An object is something that has:

\- Attributes (data)

\- Behavior (functions or methods)

Example in real life:

A "Car" has:

\- Attributes: color, model, speed

\- Behavior: start(), stop(), accelerate()

What is a Class?

A class is a blueprint or template for creating objects.

Example:

class Car:

\# attributes and methods will go here

Once a class is defined, we can create multiple objects from it.

\-------------------------------------------------

Creating a Simple Class and Object

Example:

class Car:

brand = ""

model = ""

color = ""

year = 0

price = 0.0

def show\_details(self):

print("Brand:", self.brand)

print("Model:", self.model)

\-------------------------------------------------

Constructor in Python

Constructor is used to initialize objects automatically when they are created.

Syntax:

def \_\_init\_\_(self, parameters):

\# initialize attributes

Example:

class Student:

def \_\_init\_\_(self, name, age):

self.name = name

self.age = age

s1 = Student("Ali", 20)

print(s1.name)

print(s1.age)

\-------------------------------------------------

Instance Variables vs Class Variables

\-------------------------------------------------

Instance Variables:

\- Belong to each object separately

\- Defined inside the constructor using self

Class Variables:

\- Shared among all objects

\- Defined outside the constructor, inside the class

Example:

class Student:

school\_name = "ABC High School" # class variable

def \_\_init\_\_(self, name, age):

self.name = name # instance variable

self.age = age

s1 = Student("Ali", 18)

s2 = Student("Sara", 19)

print(s1.name, "-", s1.school\_name)

print(s2.name, "-", s2.school\_name)

\-------------------------------------------------

Methods in Classes

\-------------------------------------------------

There are 3 types of methods in Python OOP:

1\. Instance Methods-------> work with object data

2\. Class Methods-------> work with class data

3\. Static Methods------> independent utility methods

Example:

class MathOperations:

count = 0

def \_\_init\_\_(self, value):

self.value = value

def square(self):

return self.value \* self.value # Instance method

@classmethod

def show\_count(cls):

print("Class variable count =", cls.count) # Class method

@staticmethod

def greet():

print("Hello from MathOperations!") # Static method

obj = MathOperations(4)

print(obj.square())

MathOperations.greet()

MathOperations.show\_count()

\-------------------------------------------------

Encapsulation (Data Hiding)

\-------------------------------------------------

Encapsulation means restricting direct access to variables and methods.

We use:

\- Single underscore \_variable -----> Protected

\- Double underscore \_\_variable --------> Private

Example:

class BankAccount:

def \_\_init\_\_(self, balance):

self.\_\_balance = balance # private variable

def deposit(self, amount):

self.\_\_balance += amount

def get\_balance(self):

return self.\_\_balance

acc = BankAccount(1000)

acc.deposit(500)

print(acc.get\_balance())

We can access private variables using name mangling (not recommended):

acc.\_BankAccount\_\_balance

\-------------------------------------------------

Inheritance

Inheritance allows one class to inherit attributes and methods from another class.

Parent Class → Base Class

Child Class → Derived Class

Example:

class Animal:

def sound(self):

print("Animals make sounds")

class Dog(Animal): # Dog inherits from Animal

def bark(self):

print("Dog barks")

d = Dog()

d.sound()

d.bark()

\-------------------------------------------------

Types of Inheritance

1\. Single Inheritance

One parent → One child

2\. Multiple Inheritance

One child → Multiple parents

3\. Multilevel Inheritance

Grandparent → Parent → Child

Example of Multilevel:

class A:

def methodA(self):

print("Method A")

class B(A):

def methodB(self):

print("Method B")

class C(B):

def methodC(self):

print("Method C")

obj = C()

obj.methodA()

obj.methodB()

obj.methodC()

\-------------------------------------------------

Polymorphism

Polymorphism means "many forms".

It allows different classes to have methods with the same name but different behavior.

Method Overriding

When a child class has the same method as the parent, the child’s method overrides the parent’s.

Example:

class Parent:

def show(self):

print("Parent method")

class Child(Parent):

def show(self):

print("Child method")

obj = Child()

obj.show()

\-------------------------------------------------

class Bird:

def sound(self):

print("Some generic bird sound")

class Sparrow(Bird):

def sound(self):

print("Chirp Chirp")

class Crow(Bird):

def sound(self):

print("Caw Caw")

birds = \[Sparrow(), Crow(), Bird()\]

for b in birds:

b.sound()

Abstraction

\-------------------------------------------------

Abstraction means showing only essential details and hiding unnecessary parts.

In Python, abstraction is achieved using \*\*Abstract Base Classes (ABC)\*\*.

Example:

from abc import ABC, abstractmethod

class Shape(ABC):

@abstractmethod

def area(self):

pass

class Circle(Shape):

def \_\_init\_\_(self, radius):

self.radius = radius

def area(self):

return 3.14 \* self.radius \* self.radius

c = Circle(5)

print("Area:", c.area())

\-------------------------------------------------

Output:

Area: 78.5

\-------------------------------------------------

15\. Practical Task

\-------------------------------------------------

TASK:

Create a class "Employee" that stores:

\- name

\- position

\- salary

Include:

\- A constructor

\- A method to display details

\- A method to give bonus (add amount to salary)

Then create 2 employee objects and display their details.

\-------------------------------------------------

HINT:

class Employee:

def \_\_init\_\_(self, name, position, salary):

self.name = name

self.position = position

self.salary = salary

def give\_bonus(self, amount):

self.salary += amount

def show(self):

print("Name:", self.name)

print("Position:", self.position)

print("Salary:", self.salary)

e1 = Employee("Ali", "Manager", 50000)

e2 = Employee("Sara", "Clerk", 30000)

e1.give\_bonus(5000)

e2.give\_bonus(2000)

e1.show()

e2.show()
