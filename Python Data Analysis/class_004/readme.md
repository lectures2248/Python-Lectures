# Pandas Class 1: Real Sales Analysis with Superstore_Sales.csv

Pandas gives us a **DataFrame**, a table with rows and columns that we control with Python code. One column of it is called a **Series**. That is all the theory we need. Open the file and start asking questions.

---

## 1. Load the file

```python
import pandas as pd

df = pd.read_csv("Superstore_Sales.csv")
```

`df` is now our table. If you get `FileNotFoundError`, give the full path:

```python
df = pd.read_csv("C:/Users/YourName/Desktop/Superstore_Sales.csv")
```

---

## 2. What does the data look like?

```python
df.head()
```

```
   Row ID        Order ID  Order Date   Ship Date       Ship Mode Customer ID  \
0       1  CA-2017-152156  08/11/2017  11/11/2017    Second Class    CG-12520
1       2  CA-2017-152156  08/11/2017  11/11/2017    Second Class    CG-12520
2       3  CA-2017-138688  12/06/2017  16/06/2017    Second Class    DV-13045
3       4  US-2016-108966  11/10/2016  18/10/2016  Standard Class    SO-20335
4       5  US-2016-108966  11/10/2016  18/10/2016  Standard Class    SO-20335

     Customer Name    Segment        Country             City       State  ...
0      Claire Gute   Consumer  United States        Henderson    Kentucky
1      Claire Gute   Consumer  United States        Henderson    Kentucky
2  Darrin Van Huff  Corporate  United States      Los Angeles  California
3   Sean O'Donnell   Consumer  United States  Fort Lauderdale     Florida
4   Sean O'Donnell   Consumer  United States  Fort Lauderdale     Florida
```

- `head()` shows the first 5 rows. `head(10)` for ten, `tail()` for the last ones.
- The `0,1,2,3,4` on the left is the index. Pandas numbers rows automatically.
- Rows 0 and 1 have the same Order ID. That is not a mistake.

**One row is one product inside an order. It is not one order.** Keep this in mind for the whole class.

---

## 3. How big is it? What columns?

```python
df.shape
```

```
(9800, 18)
```

9,800 rows and 18 columns. `shape` takes no brackets.

```python
df.columns
```

```
Index(['Row ID', 'Order ID', 'Order Date', 'Ship Date', 'Ship Mode',
       'Customer ID', 'Customer Name', 'Segment', 'Country', 'City', 'State',
       'Postal Code', 'Region', 'Product ID', 'Category', 'Sub-Category',
       'Product Name', 'Sales'],
      dtype='object')
```

| Group | Columns |
|---|---|
| Order | Row ID, Order ID, Order Date, Ship Date, Ship Mode |
| Customer | Customer ID, Customer Name, Segment |
| Location | Country, City, State, Postal Code, Region |
| Product | Product ID, Category, Sub-Category, Product Name |
| Money | Sales |

Only `Sales` holds money. Type column names exactly as written. `df["sales"]` fails, `df["Sales"]` works.

---

## 4. Data types

```python
df.info()
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 9800 entries, 0 to 9799
Data columns (total 18 columns):
 #   Column         Non-Null Count  Dtype
---  ------         --------------  -----
 0   Row ID         9800 non-null   int64
 1   Order ID       9800 non-null   object
 2   Order Date     9800 non-null   object
 3   Ship Date      9800 non-null   object
 4   Ship Mode      9800 non-null   object
 5   Customer ID    9800 non-null   object
 6   Customer Name  9800 non-null   object
 7   Segment        9800 non-null   object
 8   Country        9800 non-null   object
 9   City           9800 non-null   object
 10  State          9800 non-null   object
 11  Postal Code    9789 non-null   float64
 12  Region         9800 non-null   object
 13  Product ID     9800 non-null   object
 14  Category       9800 non-null   object
 15  Sub-Category   9800 non-null   object
 16  Product Name   9800 non-null   object
 17  Sales          9800 non-null   float64
dtypes: float64(2), int64(1), object(15)
```

`object` means text, `int64` whole number, `float64` decimal.

Two problems visible here:

1. `Order Date` and `Ship Date` are text, not dates. We fix this in step 14.
2. `Postal Code` shows 9789 out of 9800. Eleven values are missing.

---

## 5. Missing values

```python
df.isnull().sum()
```

```
Row ID            0
Order ID          0
Order Date        0
Ship Date         0
Ship Mode         0
Customer ID       0
Customer Name     0
Segment           0
Country           0
City              0
State             0
Postal Code      11
Region            0
Product ID        0
Category          0
Sub-Category      0
Product Name      0
Sales             0
dtype: int64
```

`isnull()` gives True or False for every cell. `sum()` counts the Trues, because True counts as 1.

Which rows?

```python
df[df["Postal Code"].isnull()][["City", "State", "Postal Code"]]
```

```
            City    State  Postal Code
2234  Burlington  Vermont          NaN
5274  Burlington  Vermont          NaN
8798  Burlington  Vermont          NaN
9146  Burlington  Vermont          NaN
9147  Burlington  Vermont          NaN
...
```

All 11 are Burlington, Vermont. We are not analysing postal codes, so we leave them.

---

## 6. Duplicates

```python
df.duplicated().sum()
```

```
0
```

Zero, but this check is wrong. `duplicated()` compares all 18 columns, and `Row ID` is different in every row. Remove it and check again:

```python
df.drop(columns=["Row ID"]).duplicated().sum()
```

```
1
```

```python
df[df.drop(columns=["Row ID"]).duplicated(keep=False)]
```

```
      Row ID        Order ID                 Product Name    Sales
3405    3406  CA-2017-129714  Avery Non-Stick Binders     281.372
3406    3407  CA-2017-129714  Avery Non-Stick Binders     281.372
```

`keep=False` shows both copies instead of one. One duplicate out of 9800 will not change our results.

---

## 7. Selecting columns and rows

```python
df["Sales"].head()
```

```
0    261.9600
1    731.9400
2     14.6200
3    957.5775
4     22.3680
Name: Sales, dtype: float64
```

```python
df[["Customer Name", "City", "Sales"]].head()
```

```
     Customer Name             City     Sales
0      Claire Gute        Henderson  261.9600
1      Claire Gute        Henderson  731.9400
2  Darrin Van Huff      Los Angeles   14.6200
3   Sean O'Donnell  Fort Lauderdale  957.5775
4   Sean O'Donnell  Fort Lauderdale   22.3680
```

One column, single brackets. Many columns, double brackets.

Rows by position:

```python
df.iloc[0]        # first row
df.iloc[0:3]      # rows 0, 1, 2
```

Rows by condition:

```python
df[df["State"] == "California"].shape
```

```
(1946, 18)
```

Two conditions together. Use `&` for and, `|` for or, and bracket each condition:

```python
df[(df["Region"] == "West") & (df["Sales"] > 1000)].shape
```

```
(157, 18)
```

**Practice:** How many Technology rows in New York state?

```python
df[(df["Category"] == "Technology") & (df["State"] == "New York")].shape
```

```
(204, 18)
```

---

## 8. Orders and customers

```python
df["Order ID"].nunique()
```

```
4922
```

```python
df["Customer ID"].nunique()
```

```
793
```

793 customers, 4,922 orders, 9,800 product lines. About 6 orders per customer and 2 products per order. Repeat customers.

All columns at once:

```python
df.nunique()
```

```
Row ID           9800
Order ID         4922
Order Date       1230
Ship Date        1326
Ship Mode           4
Customer ID       793
Customer Name     793
Segment             3
Country             1
City              529
State              49
Postal Code       626
Region              4
Product ID       1861
Category            3
Sub-Category       17
Product Name     1849
Sales            5757
dtype: int64
```

- `Country` has 1 value. The column is useless, drop it from your thinking.
- `Category` 3, `Sub-Category` 17, `Region` 4, `Segment` 3. Small numbers, so these are our grouping columns.
- 49 states, 529 cities.

```python
df["Customer Name"].value_counts().head(10)
```

```
Customer Name
William Brown          35
Matt Abelman           34
Paul Prost             34
John Lee               33
Jonathan Doherty       32
Chloris Kastensmidt    32
Seth Vernon            32
Emily Phan             31
Zuschuss Carroll       31
Arthur Prichep         31
Name: count, dtype: int64
```

`value_counts()` counts each value and sorts automatically. Note: 35 rows means 35 product lines, not 35 orders.

---

## 9. Categories and sub-categories

```python
df["Category"].unique()
```

```
array(['Furniture', 'Office Supplies', 'Technology'], dtype=object)
```

```python
df["Category"].value_counts()
```

```
Category
Office Supplies    5909
Furniture          2078
Technology         1813
Name: count, dtype: int64
```

Office Supplies has more rows than the other two combined. Sold most often does not mean earned most. We check that in step 11.

```python
df["Sub-Category"].value_counts()
```

```
Sub-Category
Binders        1492
Paper          1338
Furnishings     931
Phones          876
Storage         832
Art             785
Accessories     756
Chairs          607
Appliances      459
Labels          357
Tables          314
Envelopes       248
Bookcases       226
Fasteners       214
Supplies        184
Machines        115
Copiers          66
Name: count, dtype: int64
```

```python
df.groupby("Category")["Sub-Category"].unique()
```

```
Category
Furniture                    [Bookcases, Chairs, Tables, Furnishings]
Office Supplies    [Labels, Storage, Art, Binders, Appliances, Pa...
Technology                [Phones, Accessories, Machines, Copiers]
Name: Sub-Category, dtype: object
```

First `groupby`. It splits rows into piles by Category, does something to each pile, and joins the answers.

```python
df["Segment"].value_counts()
```

```
Segment
Consumer       5101
Corporate      2953
Home Office    1746
Name: count, dtype: int64
```

---

## 10. Regions, states, cities

```python
df["Region"].value_counts()
```

```
Region
West       3140
East       2785
Central    2277
South      1598
Name: count, dtype: int64
```

```python
df["State"].value_counts().head(10)
```

```
State
California        1946
New York          1097
Texas              973
Pennsylvania       582
Washington         504
Illinois           483
Ohio               454
Florida            373
Michigan           253
North Carolina     247
Name: count, dtype: int64
```

```python
df["City"].value_counts().head(10)
```

```
City
New York City    891
Los Angeles      728
Philadelphia     532
San Francisco    500
Seattle          426
Houston          374
Chicago          308
Columbus         221
San Diego        170
Springfield      161
Name: count, dtype: int64
```

California alone is 20% of all rows. Until now we only counted rows. Now the money.

---

## 11. Sales numbers

```python
round(df["Sales"].sum(), 2)
```

```
2261536.78
```

```python
df["Sales"].describe()
```

```
count     9800.000000
mean       230.769059
std        626.651875
min          0.444000
25%         17.248000
50%         54.490000
75%        210.605000
max      22638.480000
Name: Sales, dtype: float64
```

- Average sale $231, but median only $55.
- Smallest sale $0.44, largest $22,638.
- Half of all sales sit between $17 and $211.

Mean is four times the median, so a few very large sales are pulling the average up. When mean and median are far apart, go find the extreme values.

```python
df.loc[df["Sales"].idxmax()]
```

```
Order ID                                          CA-2015-145317
Order Date                                            18/03/2015
Customer Name                                        Sean Miller
Segment                                              Home Office
City                                                Jacksonville
State                                                    Florida
Region                                                     South
Category                                              Technology
Sub-Category                                            Machines
Product Name     Cisco TelePresence System EX90 Videoconferenci...
Sales                                                   22638.48
```

`idxmax()` gives the index of the largest value, `loc[]` fetches that row.

```python
(df["Sales"] > 1000).sum()
```

```
462
```

Under 5% of rows, carrying a large share of revenue.

---

## 12. Which category earns most?

```python
df.groupby("Category")["Sales"].sum().sort_values(ascending=False)
```

```
Category
Technology         827455.87
Furniture          728658.58
Office Supplies    705422.33
Name: Sales, dtype: float64
```

Office Supplies had the most rows but earns the least. Technology had the fewest rows and earns the most.

Code breakdown:
- `groupby("Category")` splits into 3 piles
- `["Sales"]` takes only the Sales column
- `.sum()` adds within each pile
- `.sort_values(ascending=False)` biggest first

```python
df.groupby("Category")["Sales"].agg(["sum", "mean", "count"]).round(2)
```

```
                       sum    mean  count
Category
Furniture        728658.58  350.65   2078
Office Supplies  705422.33  119.38   5909
Technology       827455.87  456.40   1813
```

Now the reason is clear. Technology averages $456 per sale, Office Supplies only $119.

Share of revenue:

```python
category_sales = df.groupby("Category")["Sales"].sum()
(category_sales / category_sales.sum() * 100).round(1)
```

```
Category
Furniture          32.2
Office Supplies    31.2
Technology         36.6
Name: Sales, dtype: float64
```

By segment:

```python
df.groupby("Segment")["Sales"].sum().sort_values(ascending=False).round(2)
```

```
Segment
Consumer       1148060.53
Corporate       688494.07
Home Office     424982.18
Name: Sales, dtype: float64
```

---

## 13. Best and worst sub-categories

```python
df.groupby("Sub-Category")["Sales"].sum().sort_values(ascending=False).head(10).round(2)
```

```
Sub-Category
Phones         327782.45
Chairs         322822.73
Storage        219343.39
Tables         202810.63
Binders        200028.78
Machines       189238.63
Accessories    164186.70
Copiers        146248.09
Bookcases      113813.20
Appliances     104618.40
Name: Sales, dtype: float64
```

Phones is the best sub-category. Copiers made $146,248 from only 66 sales, about $2,200 each.

```python
df.groupby("Sub-Category")["Sales"].sum().sort_values().head(5).round(2)
```

```
Sub-Category
Fasteners     3001.96
Labels       12347.73
Envelopes    16128.05
Art          26705.41
Supplies     46420.31
Name: Sales, dtype: float64
```

Remove `ascending=False` to get smallest first. Fasteners: 214 sales, $14 each.

By average instead of total:

```python
df.groupby("Sub-Category")["Sales"].mean().sort_values(ascending=False).head(5).round(2)
```

```
Sub-Category
Copiers      2215.88
Machines     1645.55
Tables        645.89
Chairs        531.83
Bookcases     503.60
Name: Sales, dtype: float64
```

Copiers jumps to number one. Total and average answer different questions.

**Practice:** Highest average sale inside Furniture only?

```python
furniture = df[df["Category"] == "Furniture"]
furniture.groupby("Sub-Category")["Sales"].mean().sort_values(ascending=False)
```

```
Sub-Category
Tables         645.89
Chairs         531.83
Bookcases      503.60
Furnishings     95.82
Name: Sales, dtype: float64
```

Filter first, then group.

---

## 14. Sales by region, state, city

```python
df.groupby("Region")["Sales"].sum().sort_values(ascending=False).round(2)
```

```
Region
West       710219.68
East       669518.73
Central    492646.91
South      389151.46
Name: Sales, dtype: float64
```

```python
df.groupby("State")["Sales"].sum().sort_values(ascending=False).head(10).round(2)
```

```
State
California      446306.46
New York        306361.15
Texas           168572.53
Washington      135206.85
Pennsylvania    116276.65
Florida          88436.53
Illinois         79236.52
Michigan         76136.07
Ohio             75130.35
Virginia         70636.72
Name: Sales, dtype: float64
```

California plus New York is one third of the whole business.

```python
df.groupby("City")["Sales"].sum().sort_values(ascending=False).head(10).round(2)
```

```
City
New York City    252462.55
Los Angeles      173420.18
Seattle          116106.32
San Francisco    109041.12
Philadelphia     108841.75
Houston           63956.14
Chicago           47820.13
San Diego         47521.03
Jacksonville      44713.18
Detroit           42446.94
Name: Sales, dtype: float64
```

Seattle is 5th by row count but 3rd by money. Fewer sales, bigger ones.

Two groupings at once:

```python
df.pivot_table(index="Region", columns="Category", values="Sales", aggfunc="sum").round(2)
```

```
Category  Furniture  Office Supplies  Technology
Region
Central   160317.46        163590.24   168739.21
East      206461.39        199940.81   263116.53
South     116531.48        124424.77   148195.21
West      245348.25        217466.51   247404.93
```

- `index` goes down the side
- `columns` goes across the top
- `values` fills the cells
- `aggfunc` is how to combine them, also `"mean"` or `"count"`

East sells much more Technology than Furniture. West is balanced.

---

## 15. Top products and customers

```python
df.groupby("Product Name")["Sales"].sum().sort_values(ascending=False).head(10).round(2)
```

```
Product Name
Canon imageCLASS 2200 Advanced Copier                                        61599.82
Fellowes PB500 Electric Punch Plastic Comb Binding Machine with Manual Bind  27453.38
Cisco TelePresence System EX90 Videoconferencing Unit                        22638.48
HON 5400 Series Task Chairs for Big and Tall                                 21870.58
GBC DocuBind TL300 Electric Binding System                                   19823.48
GBC Ibimaster 500 Manual ProClick Binding System                             19024.50
Hewlett Packard LaserJet 3310 Copier                                         18839.69
HP Designjet T520 Inkjet Large Format Printer - 24" Color                    18374.90
GBC DocuBind P400 Electric Binding System                                    17965.07
High Speed Automatic Electric Letter Opener                                  17030.31
Name: Sales, dtype: float64
```

All expensive machines. The Cisco unit at $22,638 was sold only once, and it still reached number three.

```python
df["Product Name"].value_counts().head(10)
```

```
Product Name
Staple envelope                                              47
Staples                                                      46
Easy-staple paper                                            44
Avery Non-Stick Binders                                      20
Staple remover                                               18
Staples in misc. colors                                      18
Storex Dura Pro Binders                                      17
KI Adjustable-Height Table                                   17
Staple-based wall hangings                                   16
Logitech 910-002974 M325 Wireless Mouse for Web Scrolling    15
Name: count, dtype: int64
```

Different list completely. Best earners are expensive and rare. Most popular are cheap and frequent.

```python
df.groupby("Customer Name")["Sales"].sum().sort_values(ascending=False).head(10).round(2)
```

```
Customer Name
Sean Miller           25043.05
Tamara Chand          19052.22
Raymond Buch          15117.34
Tom Ashbrook          14595.62
Adrian Barton         14473.57
Ken Lonsdale          14175.23
Sanjit Chand          14142.33
Hunter Lopez          12873.30
Sanjit Engle          12209.44
Christopher Conant    12129.07
Name: Sales, dtype: float64
```

Sean Miller is on top, but $22,638 of his $25,043 came from that one Cisco order. He is a one-time big spender, not a loyal customer. The number was correct, the conclusion would have been wrong.

---

## 16. Dates

Try it before converting:

```python
df["Order Date"].dt.year
```

```
AttributeError: Can only use .dt accessor with datetimelike values
```

It is still text. Convert both date columns:

```python
df["Order Date"] = pd.to_datetime(df["Order Date"], dayfirst=True)
df["Ship Date"] = pd.to_datetime(df["Ship Date"], dayfirst=True)
```

`dayfirst=True` is important here. Our dates are `08/11/2017`, meaning 8 November. Without it Pandas reads the American way and gives wrong months.

```python
df["Order Date"].min(), df["Order Date"].max()
```

```
(Timestamp('2015-01-03 00:00:00'), Timestamp('2018-12-30 00:00:00'))
```

Four years of data.

```python
df["Year"] = df["Order Date"].dt.year
df["Month"] = df["Order Date"].dt.month
df["Day Name"] = df["Order Date"].dt.day_name()
df[["Order Date", "Year", "Month", "Day Name"]].head()
```

```
  Order Date  Year  Month  Day Name
0 2017-11-08  2017     11 Wednesday
1 2017-11-08  2017     11 Wednesday
2 2017-06-12  2017      6    Monday
3 2016-10-11  2016     10   Tuesday
4 2016-10-11  2016     10   Tuesday
```

`.dt` opens the date. It also gives `.day`, `.month_name()`, `.quarter`. Assigning to a new name creates a new column.

Shipping time:

```python
(df["Ship Date"] - df["Order Date"]).dt.days.mean()
```

```
3.96
```

About 4 days. This was impossible while the columns were text.

---

## 17. Sales over time

```python
df.groupby("Year")["Sales"].sum().round(2)
```

```
Year
2015    479856.21
2016    459436.01
2017    600192.55
2018    722052.02
Name: Sales, dtype: float64
```

2016 dropped, then strong growth. About 50% growth overall.

```python
df.groupby("Year")["Order ID"].nunique()
```

```
Year
2015     947
2016    1019
2017    1295
2018    1661
Name: Order ID, dtype: int64
```

In 2016 orders went up but revenue went down. Customers bought more often but spent less per order.

```python
df.groupby("Month")["Sales"].sum().round(2)
```

```
Month
1      94291.63
2      59371.12
3     197573.59
4     136283.00
5     154086.72
6     145837.52
7     145535.69
8     157315.93
9     300103.41
10    199496.29
11    350161.71
12    321480.17
Name: Sales, dtype: float64
```

November is the best month, February the worst. September to December carry the business.

Month by month across all four years:

```python
monthly = df.groupby(df["Order Date"].dt.to_period("M"))["Sales"].sum().round(2)
monthly.head(12)
```

```
Order Date
2015-01    14205.71
2015-02     4519.89
2015-03    55205.80
2015-04    27906.86
2015-05    23644.30
2015-06    34322.94
2015-07    33781.54
2015-08    27117.54
2015-09    81623.53
2015-10    31453.39
2015-11    77907.66
2015-12    68167.06
Freq: M, Name: Sales, dtype: float64
```

`to_period("M")` turns a date into a `2015-01` label. Notice we grouped by a calculated Series, not a column. Pandas allows that.

```python
monthly.tail(6)
```

```
Order Date
2018-07     44825.10
2018-08     62837.85
2018-09     86152.89
2018-10     77448.13
2018-11    117938.16
2018-12     83030.39
Freq: M, Name: Sales, dtype: float64
```

November 2018 is the strongest month in the whole file.

Which category caused the 2016 dip?

```python
df.pivot_table(index="Year", columns="Category", values="Sales", aggfunc="sum").round(2)
```

```
Category  Furniture  Office Supplies  Technology
Year
2015      156477.88        149512.82   173865.51
2016      164053.87        133124.41   162257.73
2017      195813.04        182417.57   221961.94
2018      212313.79        240367.54   269370.69
```

Furniture actually grew in 2016. Office Supplies and Technology fell. And Office Supplies went 133k to 182k to 240k, the fastest growing category even though it earns the least overall.

**Practice:** Total West region sales in 2018?

```python
df[(df["Region"] == "West") & (df["Year"] == 2018)]["Sales"].sum().round(2)
```

```
248130.93
```

---
