# Python OOP + File Handling Tasks (with Solutions)

## Task 1: Student Record System

### Objective
Create a `Student` class.

**Attributes**
- Student ID
- Student Name
- Age
- Course

**Methods**
- `input_data()`
- `display_data()`
- `save_to_file()`

**Requirements**
1. Take input from the user.
2. Display student information.
3. Save data into `students.txt`.

### Solution

```python
class Student:

    def __init__(self):
        self.student_id = ""
        self.name = ""
        self.age = ""
        self.course = ""

    def input_data(self):
        self.student_id = input("Enter ID: ")
        self.name = input("Enter Name: ")
        self.age = input("Enter Age: ")
        self.course = input("Enter Course: ")

    def display_data(self):
        print("ID:", self.student_id)
        print("Name:", self.name)
        print("Age:", self.age)
        print("Course:", self.course)

    def save_to_file(self):
        with open("students.txt","a") as file:
            file.write(f"{self.student_id},{self.name},{self.age},{self.course}\n")

student = Student()
student.input_data()
student.display_data()
student.save_to_file()
print("Student saved successfully.")
```

---

## Task 2: Employee Management System

### Attributes
- Employee ID
- Name
- Department
- Salary

### Methods
- `input_employee()`
- `display_employee()`
- `save_employee()`

### Solution

```python
class Employee:

    def __init__(self):
        self.id=""
        self.name=""
        self.department=""
        self.salary=""

    def input_employee(self):
        self.id=input("Employee ID: ")
        self.name=input("Employee Name: ")
        self.department=input("Department: ")
        self.salary=input("Salary: ")

    def display_employee(self):
        print(self.id,self.name,self.department,self.salary)

    def save_employee(self):
        with open("employees.txt","a") as file:
            file.write(f"{self.id},{self.name},{self.department},{self.salary}\n")

emp=Employee()
emp.input_employee()
emp.display_employee()
emp.save_employee()
```

---

## Task 3: Library Book System

### Attributes
- Book ID
- Book Name
- Author
- Price

### Solution

```python
class Book:

    def __init__(self):
        self.book_id=""
        self.name=""
        self.author=""
        self.price=""

    def input_book(self):
        self.book_id=input("Book ID: ")
        self.name=input("Book Name: ")
        self.author=input("Author: ")
        self.price=input("Price: ")

    def display_book(self):
        print(self.book_id,self.name,self.author,self.price)

    def save_book(self):
        with open("books.txt","a") as file:
            file.write(f"{self.book_id},{self.name},{self.author},{self.price}\n")

book=Book()
book.input_book()
book.display_book()
book.save_book()
```

---

## Task 4: Product Inventory System (CSV)

### Attributes
- Product ID
- Product Name
- Quantity
- Price

### Solution

```python
import csv

class Product:

    def __init__(self):
        self.id=""
        self.name=""
        self.quantity=""
        self.price=""

    def input_product(self):
        self.id=input("Product ID: ")
        self.name=input("Product Name: ")
        self.quantity=input("Quantity: ")
        self.price=input("Price: ")

    def display_product(self):
        print(self.id,self.name,self.quantity,self.price)

    def save_product(self):
        with open("products.csv","a",newline="") as file:
            writer=csv.writer(file)
            writer.writerow([self.id,self.name,self.quantity,self.price])

product=Product()
product.input_product()
product.display_product()
product.save_product()
```

---

## Task 5: Bank Account System

### Attributes
- Account Number
- Customer Name
- Balance

### Methods
- `deposit()`
- `withdraw()`
- `display_balance()`
- `save_transaction()`

### Solution

```python
class BankAccount:

    def __init__(self):
        self.account_number=""
        self.customer_name=""
        self.balance=0

    def input_account(self):
        self.account_number=input("Account Number: ")
        self.customer_name=input("Customer Name: ")
        self.balance=float(input("Initial Balance: "))

    def deposit(self):
        amount=float(input("Deposit Amount: "))
        self.balance+=amount

    def withdraw(self):
        amount=float(input("Withdraw Amount: "))
        if amount<=self.balance:
            self.balance-=amount
        else:
            print("Insufficient Balance")

    def display_balance(self):
        print("Current Balance:",self.balance)

    def save_transaction(self):
        with open("transactions.txt","a") as file:
            file.write(f"{self.account_number},{self.customer_name},{self.balance}\n")

account=BankAccount()
account.input_account()
account.deposit()
account.withdraw()
account.display_balance()
account.save_transaction()
```
