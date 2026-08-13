

Less talking, more code. Every concept gets a one-line reason, then straight to code, then exercises with solutions.

---

## Part 1: NumPy → Pandas

NumPy is great for numbers only. Real data has names, cities, missing values, mixed types — NumPy can't handle that cleanly. That's why Pandas exists.

```python
import pandas as pd
```

---

## Part 2: Series (one column)

```python
ages = pd.Series([20, 22, 25, 28])
print(ages)
```
```
0    20
1    22
2    25
3    28
dtype: int64
```

```python
print(ages.index)    # RangeIndex(start=0, stop=4, step=1)
print(ages.values)   # [20 22 25 28]
print(ages.dtype)    # int64
print(ages.shape)    # (4,)
```

From a dictionary (keys become the index):

```python
salary = pd.Series({"Ali": 50000, "Ahmed": 70000, "Sara": 60000})
print(salary)
```

Custom index on a list:

```python
marks = pd.Series([85, 72, 91], index=["Ali", "Ahmed", "Sara"])
print(marks["Ahmed"])     # 72
marks["Ahmed"] = 78       # update a value
print(marks)
```

### Exercises

**1. Create a salary Series for Bilal, Hina, Kamran, Zara → 45000, 62000, 58000, 71000. Print Kamran's salary.**
```python
salaries = pd.Series([45000, 62000, 58000, 71000],
                      index=["Bilal", "Hina", "Kamran", "Zara"])
print(salaries["Kamran"])   # 58000
```

**2. Create a Series from this dict, print index and values separately.**
```python
prices = pd.Series({"Laptop": 80000, "Mobile": 45000, "Tablet": 30000})
print(prices.index)
print(prices.values)
```

**3. Change Bilal's salary to 50000 in the Series from Exercise 1.**
```python
salaries["Bilal"] = 50000
print(salaries)
```

---

## Part 3: DataFrame (full table)

```python
data = {
    "Name": ["Ali", "Ahmed", "Sara", "Usman"],
    "Age": [22, 25, 24, 27],
    "City": ["Karachi", "Lahore", "Karachi", "Islamabad"],
    "Salary": [50000, 70000, 60000, 90000]
}
df = pd.DataFrame(data)
print(df)
```
```
    Name  Age       City  Salary
0    Ali   22    Karachi   50000
1  Ahmed   25     Lahore   70000
2   Sara   24    Karachi   60000
3  Usman   27  Islamabad   90000
```

`df["Name"]` alone → a Series. A DataFrame is just multiple Series sharing one index.

Two more ways to build one:

```python
# list of dictionaries
records = [
    {"Name": "Ali", "Age": 22, "City": "Karachi"},
    {"Name": "Ahmed", "Age": 25, "City": "Lahore"}
]
df2 = pd.DataFrame(records)

# numpy array + column names
arr = np.array([[22, 50000], [25, 70000]])
df3 = pd.DataFrame(arr, columns=["Age", "Salary"])
```

---

## Part 4: DataFrame attributes

```python
print(df.index)      # row labels
print(df.columns)    # column names
print(df.values)     # raw data (numpy array)
print(df.dtypes)     # data type per column
print(df.shape)      # (rows, columns)
print(df.size)       # total cells = rows * columns
```

**Exercise: How many rows and columns? What type is Salary stored as?**
```python
print(df.shape[0], df.shape[1])
print(df.dtypes["Salary"])
```

---

## Part 5: Inspecting real data

Use one bigger dataset for the rest of the lecture:

```python
employee_data = {
    "Name": ["Ali","Ahmed","Sara","Usman","Hina","Bilal","Ayesha","Kamran","Zara","Fahad"],
    "Age": [22,25,24,27,29,23,31,26,28,24],
    "Department": ["IT","Sales","HR","IT","Finance","Sales","HR","IT","Finance","Sales"],
    "City": ["Karachi","Lahore","Karachi","Islamabad","Karachi","Lahore","Islamabad","Karachi","Lahore","Karachi"],
    "Salary": [50000,70000,60000,90000,85000,55000,72000,95000,68000,58000],
    "Experience": [1,3,2,5,6,2,7,4,3,1]
}
df = pd.DataFrame(employee_data)
```

```python
print(df.head())       # first 5 rows
print(df.head(3))      # first 3 rows
print(df.tail())        # last 5 rows
print(df.tail(2))       # last 2 rows
df.info()               # columns, non-null counts, dtypes
print(df.describe())    # count, mean, std, min, max, percentiles
```

`info()` tells you if any values are missing. `describe()` gives stats on numeric columns only.

**Exercise: Print first 4 rows and last 3 rows.**
```python
print(df.head(4))
print(df.tail(3))
```

**Exercise: Any missing values in Salary?**
```python
df.info()   # Salary shows 10 non-null out of 10 rows → no missing values
```

---

## Part 6: Loading real data

```python
df = pd.read_csv("employees.csv")
print(df.head())
df.info()
```

CSV file content (save this as `employees.csv`):
```
Name,Age,Department,City,Salary,Experience
Ali,22,IT,Karachi,50000,1
Ahmed,25,Sales,Lahore,70000,3
Sara,24,HR,Karachi,60000,2
Usman,27,IT,Islamabad,90000,5
```

Other formats:
```python
pd.read_excel("employees.xlsx")
pd.read_json("employees.json")
```

**Exercise: You just ran `pd.read_csv("sales.csv")`. Write the checks to confirm it loaded correctly.**
```python
df = pd.read_csv("sales.csv")
print(df.head())
df.info()
```

---

## Part 7: Column selection

```python
print(df["Name"])              # single column → Series
print(df[["Name", "Salary"]])  # multiple columns → DataFrame (note double brackets)
```

**Exercise: Display only Salary.**
```python
print(df["Salary"])
```

**Exercise: Display Name and City together.**
```python
print(df[["Name", "City"]])
```

**Exercise: Display Name, Age, Salary. Check the type of the result.**
```python
result = df[["Name", "Age", "Salary"]]
print(result)
print(type(result))   # DataFrame
```

**Exercise: Select only Department and check its type.**
```python
dept = df["Department"]
print(type(dept))   # Series
```

---

## Part 8: Row selection

```python
print(df.iloc[0])          # first row, by position
print(df.iloc[0:3])        # first 3 rows
print(df.iloc[0, 1])       # row 0, column 1 (Age) → 22
print(df.iloc[0:3, 0:2])   # rows 0-2, columns Name & Age

print(df.loc[0])           # first row, by label
```

`iloc` = position always. `loc` = label always. They look the same here because our index is 0,1,2... but with a custom index (like names) only `loc` would still work with the labels.

**Exercise: Get the 4th row (position 3) using iloc.**
```python
print(df.iloc[3])
```

**Exercise: Get rows 2 to 5, only Name and Salary columns.**
```python
print(df.iloc[2:6, [0, 4]])
```

---

## Part 9: Filtering data

```python
print(df["Age"] > 25)          # boolean Series (True/False)
print(df[df["Age"] > 25])      # actual filtered rows
```

```python
print(df[df["Salary"] > 70000])
print(df[df["City"] == "Karachi"])

# multiple conditions: use & and |, wrap each condition in ()
print(df[(df["Age"] > 25) & (df["Salary"] > 70000)])
print(df[(df["City"] == "Karachi") | (df["City"] == "Lahore")])
```

Use `&` / `|` not `and` / `or` — Pandas conditions return a whole column of True/False, and only `&`/`|` can combine those correctly. Always use parentheses around each condition.

### Exercises (all solved)

**Salary > 80,000**
```python
print(df[df["Salary"] > 80000])
```

**All employees from Karachi**
```python
print(df[df["City"] == "Karachi"])
```

**Age > 25 and Salary > 70000**
```python
print(df[(df["Age"] > 25) & (df["Salary"] > 70000)])
```

**Department is IT or Finance**
```python
print(df[(df["Department"] == "IT") | (df["Department"] == "Finance")])
```

**Experience > 3, show only Name and Department**
```python
result = df[df["Experience"] > 3]
print(result[["Name", "Department"]])
```

---

## Part 10: Mini scenario — Sales data

```python
sales_data = {
    "Product": ["Laptop","Mobile","Tablet","Headphones","Keyboard","Mouse","Monitor","Printer","Speaker","Camera"],
    "Category": ["Electronics","Electronics","Electronics","Accessories","Accessories","Accessories","Electronics","Electronics","Accessories","Electronics"],
    "Price": [80000,45000,30000,3500,2000,1500,25000,15000,5000,60000],
    "Quantity": [5,12,8,20,15,30,6,4,10,3],
    "City": ["Karachi","Lahore","Karachi","Islamabad","Karachi","Lahore","Islamabad","Karachi","Lahore","Karachi"]
}
sales_df = pd.DataFrame(sales_data)
```

**Products above 5000**
```python
print(sales_df[sales_df["Price"] > 5000])
```

**Sales from Karachi**
```python
print(sales_df[sales_df["City"] == "Karachi"])
```

**Quantity > 10**
```python
print(sales_df[sales_df["Quantity"] > 10])
```

**Price > 5000 AND Quantity > 10**
```python
print(sales_df[(sales_df["Price"] > 5000) & (sales_df["Quantity"] > 10)])
```

**Only Product and Price**
```python
print(sales_df[["Product", "Price"]])
```

**Shape of dataset**
```python
print(sales_df.shape)   # (10, 5)
```

---

## Part 11: Common mistakes (quick fixes)

| Wrong | Right | Why |
|---|---|---|
| `df.head` | `df.head()` | method needs `()` to run |
| `df.shape()` | `df.shape` | attribute, no `()` |
| `df[df["Age"]>25 and df["Salary"]>50000]` | `df[(df["Age"]>25) & (df["Salary"]>50000)]` | use `&`, wrap each condition |
| `df["Name", "Salary"]` | `df[["Name", "Salary"]]` | need list → double brackets |

---

## Part 12: Final Integrated Practice (fully solved)

```python
company_data = {
    "Name": ["Adeel","Nimra","Waqas","Sana","Junaid","Mahnoor","Imran","Rabia","Talha","Sadia","Farhan","Iqra"],
    "Department": ["IT","Sales","HR","IT","Finance","Sales","HR","IT","Finance","Sales","IT","HR"],
    "Age": [23,26,30,24,33,27,29,25,31,22,28,35],
    "City": ["Karachi","Lahore","Karachi","Islamabad","Karachi","Lahore","Islamabad","Karachi","Lahore","Karachi","Islamabad","Karachi"],
    "Salary": [52000,61000,75000,58000,98000,64000,71000,88000,92000,49000,81000,76000],
    "YearsExperience": [1,3,6,2,8,4,5,4,7,1,5,9],
    "PerformanceRating": [4,3,5,3,5,4,4,5,4,2,4,5]
}
df = pd.DataFrame(company_data)
```

**1. Print the DataFrame**
```python
print(df)
```

**2. Shape, columns, dtypes**
```python
print(df.shape)
print(df.columns)
print(df.dtypes)
```

**3. Head and tail**
```python
print(df.head())
print(df.tail(3))
```

**4. Full summary**
```python
df.info()
print(df.describe())
```

**5. Name and Salary columns only**
```python
print(df[["Name", "Salary"]])
```

**6. Row at position 5 (iloc and loc)**
```python
print(df.iloc[5])
print(df.loc[5])
```

**7. Salary > 80,000**
```python
print(df[df["Salary"] > 80000])
```

**8. IT department AND experience > 4 years**
```python
print(df[(df["Department"] == "IT") & (df["YearsExperience"] > 4)])
```

**9. HR department OR rating = 5**
```python
print(df[(df["Department"] == "HR") | (df["PerformanceRating"] == 5)])
```

**10. Business question: Karachi + experience > 3 + rating >= 4 → leadership program candidates. Show Name, Department, YearsExperience.**
```python
candidates = df[
    (df["City"] == "Karachi") &
    (df["YearsExperience"] > 3) &
    (df["PerformanceRating"] >= 4)
]
print(candidates[["Name", "Department", "YearsExperience"]])
```
```
     Name Department  YearsExperience
2   Waqas         HR                6
4  Junaid    Finance                8
7   Rabia         IT                4
11   Iqra         HR                9
```
