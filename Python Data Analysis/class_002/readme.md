Class 2 - Indexing, Slicing, Reshaping + List vs Array Speed Test
=====================================================================

Last class we learned what data, science, and data science mean, and we saw
how to create 1D, 2D, and 3D arrays in NumPy along with their attributes
(`.shape`, `.ndim`, `.dtype`, `.size`).

Today we learn how to actually **pull out specific values** from an array
(indexing), **pull out a range of values** (slicing), and **change the shape**
of an array (reshaping). We'll also prove with real numbers why NumPy is
faster than a plain Python list.


1. Indexing - Picking One Value from an Array
---------------------------------------------------

Indexing means using a position number to grab one specific value.
Remember - counting starts from **0**, not 1.

**Example 1 - 1D array:**

```python
import numpy as np

prices = np.array([100, 200, 300, 400, 500])
print(prices[0])   
print(prices[2])   
print(prices[-1])  
```

Output:
```
100
300
500
```

`prices[0]` is the first value, `prices[2]` is the third value, and `prices[-1]`
means "last value" - negative indexing counts from the end.

**Example 2 - Student marks:**

```python
marks = np.array([78, 85, 92, 60, 74])
print("First student's marks:", marks[0])
print("Last student's marks:", marks[-1])
```

Output:
```
First student's marks: 78
Last student's marks: 74
```

**Example 3 - 2D array (rows and columns):**

```python
sales = np.array([[100, 150, 200],   # Monday
                   [120, 130, 210],   # Tuesday
                   [90, 160, 190]])   # Wednesday

print(sales[0, 0])   
print(sales[1, 2])   
print(sales[2, 1])   
```

Output:
```
100
210
160
```

For 2D arrays, the format is `array[row, column]`. So `sales[1, 2]` means
"Tuesday's row, 3rd column" = 210.

**Example 4 - Getting a full row or full column:**

```python
print(sales[0])       
print(sales[:, 0])    
```

Output:
```
[100 150 200]
[100 120  90]
```

`sales[0]` gives the whole first row (Monday's sales for all products).
`sales[:, 0]` gives the whole first column (all days' sales for Product A only).
The `:` means "give me everything" along that direction.


2. Slicing - Picking a Range of Values
--------------------------------------------

Slicing means grabbing multiple values using a start and stop position.
Format: `array[start:stop]` - stop is NOT included.

**Example 1 - 1D array:**

```python
prices = np.array([100, 200, 300, 400, 500])
print(prices[1:4])   
print(prices[:3])    
print(prices[2:])    
```

Output:
```
[200 300 400]
[100 200 300]
[300 400 500]
```

`prices[1:4]` gives index 1, 2, 3 (not 4). `prices[:3]` means "from start to
index 2". `prices[2:]` means "from index 2 to the end".

**Example 2 - Last 3 days' temperature out of a week:**

```python
temps = np.array([28, 30, 32, 29, 35, 31, 33])
print(temps[-3:])
```

Output:
```
[35 31 33]
```

**Example 3 - Slicing a 2D array (specific rows and columns):**

```python
sales = np.array([[100, 150, 200],
                   [120, 130, 210],
                   [90, 160, 190]])

print(sales[0:2, 0:2])
```

Output:
```
[[100 150]
 [120 130]]
```

This grabs the first 2 rows and first 2 columns only - like cutting a small
box out of a bigger table.

**Example 4 - Only Tuesday and Wednesday sales for Product B and C:**

```python
print(sales[1:3, 1:3])
```

Output:
```
[[130 210]
 [160 190]]
```


3. Boolean Indexing - Filtering with Conditions
------------------------------------------------------

This is one of the most useful tricks in NumPy - picking values based on a
condition instead of a position.

**Example 1 - Get all prices above 250:**

```python
prices = np.array([100, 200, 300, 400, 500])
print(prices[prices > 250])
```

Output:
```
[300 400 500]
```

`prices > 250` creates a True/False array, and NumPy uses that to filter.

**Example 2 - Get all students who passed (marks >= 70):**

```python
marks = np.array([78, 85, 92, 60, 74])
passed = marks[marks >= 70]
print(passed)
```

Output:
```
[78 85 92 74]
```

**Example 3 - Get days where sales were below 150 for Product A:**

```python
product_a_sales = np.array([100, 120, 90, 200, 80])
low_sales_days = product_a_sales[product_a_sales < 150]
print(low_sales_days)
```

Output:
```
[100 120  90  80]
```


4. Reshaping - Changing the Shape of an Array
------------------------------------------------------

Reshaping means arranging the same data into a different number of rows and
columns, without changing the actual values.

**Example 1 - Turn a 1D array into a 2D array:**

```python
arr = np.arange(1, 7)
print(arr)

reshaped = arr.reshape(2, 3)
print(reshaped)
```

Output:
```
[1 2 3 4 5 6]
[[1 2 3]
 [4 5 6]]
```

We had 6 numbers in a row, now arranged as 2 rows x 3 columns. Note - the total
count must match: 2 x 3 = 6, same as before.

**Example 2 - Same 6 numbers as 3 rows x 2 columns instead:**

```python
reshaped2 = arr.reshape(3, 2)
print(reshaped2)
```

Output:
```
[[1 2]
 [3 4]
 [5 6]]
```

**Example 3 - Real use case, imagine 12 months of sales as 1 long row, reshape
into a 4-quarter x 3-month table:**

```python
monthly_sales = np.arange(1, 13)
quarterly = monthly_sales.reshape(4, 3)
print(quarterly)
```

Output:
```
[[ 1  2  3]
 [ 4  5  6]
 [ 7  8  9]
 [10 11 12]]
```

Now each row represents one quarter (3 months), which makes it easier to
calculate quarter-wise totals later.

**Example 4 - Flattening back to 1D:**

```python
flat = quarterly.flatten()
print(flat)
```

Output:
```
[ 1  2  3  4  5  6  7  8  9 10 11 12]
```

`.flatten()` takes any shape and turns it back into a single row.


5. List vs Array - The Speed Test
----------------------------------------

Let's actually prove NumPy is faster, instead of just saying it.

```python
import numpy as np
import time

size = 5_000_000

# Using a normal Python list
python_list = list(range(size))
start = time.time()
result_list = [x * 2 for x in python_list]
end = time.time()
print("Python list time:", end - start)

# Using a NumPy array
numpy_array = np.arange(size)
start = time.time()
result_array = numpy_array * 2
end = time.time()
print("NumPy array time:", end - start)
```

Sample output (will vary slightly on your machine):
```
Python list time: 0.42
NumPy array time: 0.015
```

Same task - multiply 5 million numbers by 2. NumPy finishes almost 20-30 times
faster. This is exactly why every serious data project uses NumPy/Pandas
instead of plain lists once the data gets large.


Summary
----------

- Indexing pulls out one specific value - `array[position]` for 1D,
  `array[row, column]` for 2D.
- Slicing pulls out a range - `array[start:stop]`, stop not included.
- Boolean indexing filters values using a condition, like `array[array > 250]`.
- Reshaping changes rows/columns without changing the values - total count
  must stay the same.
- `.flatten()` turns any array back into a single row.
- NumPy is dramatically faster than plain Python lists for numeric operations
  on large data - we proved it with an actual timer.


Asignment
-----------

1. Create the array `[10, 20, 30, 40, 50, 60]`. Print the value at index 3 and
   the last value using negative indexing.
2. Create a 2D array of your choice (3x3) and print only its middle row and
   middle column.
3. From the array `[45, 67, 23, 89, 12, 90, 34]`, use boolean indexing to get
   only the values greater than 40.
4. Create `np.arange(1, 13)` and reshape it into 3 rows x 4 columns, then
   flatten it back to check you get the original array.
5. Run the list vs array speed test yourself with a bigger size (like
   20,000,000) and note the time difference.


