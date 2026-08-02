Class 1 - What is Data? What is Science? + NumPy Basics
===========================================================



1. What is Data?
--------------------

Data simply means **facts and figures** - anything we can record or measure.

Examples of data:
- A student's marks: 85
- Today's temperature: 32°C
- A customer's name: "Ali"
- A shop's daily sale: Rs. 15,000
- A list of products sold: Sweets, Cake, Juice

Data can be of two types:

- **Numeric data** - numbers (marks, price, age, temperature)
- **Text/categorical data** - words or labels (name, city, product type)

By itself, data is just raw facts. It doesn't tell you anything useful until
someone studies it. That's where "science" comes in.


2. What is Science?
------------------------

Science means studying something in an organized, step-by-step way to
understand it, and using proof (evidence) instead of guessing.

Example: If you say "sugar dissolves faster in hot water," that's just a guess
until you actually test it with real water, real sugar, and note down the time.
Once you test it and get proof, it becomes scientific knowledge.

So "science" = observe -> test -> find proof -> get a reliable answer.

Now combine the two words: **Data + Science = Data Science**.
It means studying data in an organized way, using proof (numbers, statistics)
to find real answers - not guesses.


3. Data Analysis vs Data Engineering vs Data Science
--------------------------------------------------------

Now that we know what "data" and "science" mean, let's separate three related
job titles that people often confuse:

- **Data Engineer** - builds the system that collects, moves, and stores data.
  They build the pipeline so data is ready to use.
- **Data Analyst** - studies the data that's already collected, finds patterns,
  answers specific questions.
- **Data Scientist** - goes further, uses statistics and machine learning to
  predict future numbers or find hidden patterns.

**Example 1:**
A shop starts saving its daily sales into a system automatically every day.
The person who built that saving system = Data Engineer.

**Example 2:**
Someone opens that saved data and says "Karachi branch sold 30% more sweets in
Ramzan than Lahore branch." That person = Data Analyst.

**Example 3:**
Someone uses the last 3 years of that data to say "next Ramzan we'll likely
sell 5000kg more than this year." That person = Data Scientist.

Simple way to remember:
Engineer builds the pipe -> Analyst checks what's flowing in it -> Scientist
predicts what will flow tomorrow.

All three roles use Python heavily to work with data. That's why we start with
**NumPy** - it's the base library almost everything else (Pandas, ML tools) is
built on top of.


4. Why Do We Need NumPy?
-----------------------------

Python already has lists to store data. So why a separate library?

**Example 1 - Doing math with a plain list:**

```python
numbers = [1, 2, 3, 4, 5]
result = []
for n in numbers:
    result.append(n * 2)
print(result)
```

Output:
```
[2, 4, 6, 8, 10]
```

This works, but we had to write a loop just to multiply 5 numbers by 2.

**Example 2 - Same thing with NumPy:**

```python
import numpy as np

numbers = np.array([1, 2, 3, 4, 5])
result = numbers * 2
print(result)
```

Output:
```
[ 2  4  6  8 10]
```

Same result, one line, no loop needed.

**Example 3 - Why this matters with real data:**
Imagine a sales file with 1,000,000 rows and you need to give 10% discount on
every price.

```python
prices = np.array([100, 200, 300])
discounted = prices * 0.9
print(discounted)
```

Output:
```
[ 90. 180. 270.]
```

This same one line works whether it's 3 rows or 3 million rows. NumPy runs this
internally using C code, not plain Python, so it's much faster on large data.


5. Installing and Importing NumPy
--------------------------------------

```python
pip install numpy
```

```python
import numpy as np
```

`np` is the standard short name used everywhere - always import it this way so
your code matches what everyone else writes.


6. What is an Array?
------------------------

An array is a container that holds numbers in a grid, all of the same type.

- **1D array** = a single row of numbers
- **2D array** = a table (rows + columns)
- **3D array** = multiple tables stacked together


7. 1D Arrays - With Examples
----------------------------------

**Example 1 - Basic creation:**

```python
arr1d = np.array([10, 20, 30, 40, 50])
print(arr1d)
print(type(arr1d))
```

Output:
```
[10 20 30 40 50]
<class 'numpy.ndarray'>
```

**Example 2 - Daily temperature readings:**

```python
temps = np.array([30, 32, 29, 35, 31])
print("Average temp:", temps.mean())
```

Output:
```
Average temp: 31.4
```

One line gives us the average of 5 days - no manual addition or loop.

**Example 3 - Product prices:**

```python
prices = np.array([500, 1200, 750, 300])
print("Highest price:", prices.max())
print("Lowest price:", prices.min())
```

Output:
```
Highest price: 1200
Lowest price: 300
```

**Example 4 - Student marks out of 100:**

```python
marks = np.array([78, 85, 92, 60, 74])
print("Total marks:", marks.sum())
print("Average marks:", marks.mean())
```

Output:
```
Total marks: 389
Average marks: 77.8
```


8. 2D Arrays - With Examples
----------------------------------

**Example 1 - Basic grid:**

```python
arr2d = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])
print(arr2d)
```

Output:
```
[[1 2 3]
 [4 5 6]
 [7 8 9]]
```

3 rows, 3 columns.

**Example 2 - Student marks (rows = students, columns = subjects):**

```python
marks = np.array([[85, 90, 78],
                   [70, 88, 92],
                   [95, 60, 75]])
print(marks)
```

Row 1 = Student 1's marks in Math, Science, English. Just like an Excel sheet.

**Example 3 - Shop sales by day and product:**

```python
sales = np.array([[100, 150, 200],   # Monday: Product A, B, C
                   [120, 130, 210],   # Tuesday
                   [90, 160, 190]])   # Wednesday
print(sales)
```

Now you can ask things like "total sales on Monday" or "total sales of Product
A across all days" - we'll cover exactly how to pull that out in the next class
using indexing and slicing.

**Example 4 - A small attendance sheet (1 = present, 0 = absent):**

```python
attendance = np.array([[1, 1, 0],   # Student 1: Mon, Tue, Wed
                        [1, 0, 1],   # Student 2
                        [0, 1, 1]])  # Student 3
print(attendance)
```


9. 3D Arrays - With Examples
----------------------------------

**Example 1 - Basic:**

```python
arr3d = np.array([[[1, 2], [3, 4]],
                   [[5, 6], [7, 8]]])
print(arr3d)
```

Output:
```
[[[1 2]
  [3 4]]

 [[5 6]
  [7 8]]]
```

2 layers, each layer is a 2x2 grid.

**Example 2 - Two weeks of business data:**
Imagine Layer 1 = Week 1 sales (2 days x 2 products), Layer 2 = Week 2 sales
(same structure). Same shape as above, just picture real sales numbers instead
of 1,2,3,4.

**Example 3 - Where 3D really shows up - images:**
A color photo is actually stored as a 3D array - height x width x 3 (Red,
Green, Blue values for every pixel). You won't build one by hand right now,
just know this is where 3D arrays are used in real projects like image
processing.


10. Array Attributes
-------------------------

```python
arr2d = np.array([[1, 2, 3],
                   [4, 5, 6]])

print(arr2d.shape)   
print(arr2d.ndim)    
print(arr2d.dtype)   
print(arr2d.size)    
```

Output:
```
(2, 3)
2
int64
6
```

- `.shape` = rows, columns
- `.ndim` = how many dimensions (1D/2D/3D)
- `.dtype` = data type of elements
- `.size` = total number of elements

**Example - checking a sales array:**

```python
sales = np.array([[100, 150, 200],
                   [120, 130, 210],
                   [90, 160, 190]])
print(sales.shape)   
print(sales.size)    
```

Output:
```
(3, 3)
9
```

3 rows (days), 3 columns (products), 9 total values.


11. Quick Array Shortcuts
------------------------------

**Example 1 - zeros, useful as a placeholder before real data is filled in:**

```python
np.zeros((2, 3))
```
```
[[0. 0. 0.]
 [0. 0. 0.]]
```

**Example 2 - ones:**

```python
np.ones((3, 3))
```
```
[[1. 1. 1.]
 [1. 1. 1.]
 [1. 1. 1.]]
```

**Example 3 - arange, like Python's range() but returns an array:**

```python
np.arange(0, 10, 2)
```
```
[0 2 4 6 8]
```

**Example 4 - linspace, evenly spaced numbers:**

```python
np.linspace(0, 1, 5)
```
```
[0.   0.25 0.5  0.75 1.  ]
```

Gives 5 evenly spaced numbers between 0 and 1 - this becomes useful later when
plotting graphs.


12. Setting Data Type
--------------------------

```python
arr = np.array([1, 2, 3], dtype=float)
print(arr)
```
```
[1. 2. 3.]
```

**Why this matters:** If you're storing prices with decimals (like 99.5) but
NumPy guesses the type as `int`, you'll lose the decimal part. Setting
`dtype=float` avoids that problem.


Summary
----------

- Data = raw facts and figures. Science = studying something with proof, not
  guesses. Data Science = studying data with proof to find real answers.
- Data Engineer builds the pipeline, Data Analyst studies the data, Data
  Scientist predicts with it.
- NumPy arrays beat Python lists for numeric work - faster, less memory, no
  manual loops.
- 3 types of arrays: 1D (row), 2D (table), 3D (stacked tables).
- Know these 4 attributes: `.shape`, `.ndim`, `.dtype`, `.size`.
- Shortcuts to remember: `zeros()`, `ones()`, `arange()`, `linspace()`.


Assignment
-----------

1. Make a 1D array of numbers 1 to 10 using `arange()`.
2. Make a 4x4 array of all zeros.
3. Make the array `[5, 10, 15, 20]` - print its shape, dtype, and size.
4. Create your own 3x3 sales-like 2D array (any numbers you like) and print
   its shape and total number of elements.
5. Run `linspace(0, 100, 6)` and explain the output in your own words.

