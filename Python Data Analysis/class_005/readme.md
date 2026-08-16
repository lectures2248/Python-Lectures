# Pandas Class 2: Full Exploratory Data Analysis of the Superstore Data

In Class 1 we learned what this dataset is. Today we behave like an analyst who has been asked a real question by the business:

**Where does our money actually come from, and what is changing?**

Same file, deeper questions. Every new Pandas idea comes only when a question needs it.

---

## Setup

Start a fresh notebook and run this once:

```python
import pandas as pd

df = pd.read_csv("Superstore_Sales.csv")

df["Order Date"] = pd.to_datetime(df["Order Date"], dayfirst=True)
df["Ship Date"] = pd.to_datetime(df["Ship Date"], dayfirst=True)

df["Year"] = df["Order Date"].dt.year
df["Month"] = df["Order Date"].dt.month
df["Ship Days"] = (df["Ship Date"] - df["Order Date"]).dt.days

df.shape
```

```
(9800, 21)
```

18 original columns plus the 3 we created. Remember from Class 1: one row is one product inside an order.

---

## Part 1: How is the business moving over time?

### Question: Is the business growing, and by how much each year?

Total per year is not enough. A manager wants the growth percentage.

```python
yearly = df.groupby("Year")["Sales"].sum().round(2)
yearly
```

```
Year
2015    479856.21
2016    459436.01
2017    600192.55
2018    722052.02
Name: Sales, dtype: float64
```

```python
yearly.pct_change().mul(100).round(1)
```

```
Year
2015     NaN
2016    -4.3
2017    30.6
2018    20.3
Name: Sales, dtype: float64
```

`pct_change()` compares each value with the one before it. `mul(100)` turns it into a percentage.

- 2016 fell by 4.3%
- 2017 jumped 30.6%
- 2018 added another 20.3%
- 2015 shows `NaN` because there is no earlier year to compare with

So the business had one bad year and then two strong years. The next question is obvious: what happened inside 2016?

### Question: Which part of the year is strongest?

```python
df.pivot_table(index="Year", columns=df["Order Date"].dt.quarter,
               values="Sales", aggfunc="sum").round(2)
```

```
Order Date          1          2          3          4
Year
2015         73931.40   85874.09  142522.61  177528.11
2016         62357.69   87713.37  128560.21  180804.74
2017         92686.36  135061.16  138056.37  234388.65
2018        122260.88  127558.62  193815.84  278416.68
```

We passed `df["Order Date"].dt.quarter` directly as the `columns`. Pandas accepts a calculated Series there, not only a column name.

Quarter 4 is the biggest in every single year, and Quarter 1 is the smallest in every single year. This pattern repeats for four years, so it is a real seasonal pattern and not luck.

### Question: Which months drive that?

```python
df.groupby("Month")["Sales"].agg(["sum", "mean", "count"]).round(2)
```

```
             sum    mean  count
Month
1       94291.63  257.63    366
2       59371.12  199.90    297
3      197573.59  290.55    680
4      136283.00  207.43    657
5      154086.72  212.53    725
6      145837.52  211.05    691
7      145535.69  208.80    697
8      157315.93  227.01    693
9      300103.41  221.64   1354
10     199496.29  246.60    809
11     350161.71  241.66   1449
12     321480.17  232.62   1382
```

Look at `sum` and `count` together, not `sum` alone.

- November: $350,161 from 1,449 rows
- February: $59,371 from 297 rows
- March: only 680 rows but a high average of $290

November wins because of **volume**, not because sales get bigger. The average sale in November ($241) is close to the yearly average. September, November and December simply have far more orders.

March is different. It has fewer rows than May, but a higher total, because its average sale is the highest of any month.

### Question: What were the single best and worst months in four years?

```python
monthly = df.groupby(df["Order Date"].dt.to_period("M"))["Sales"].sum().round(2)
monthly.nlargest(5)
```

```
Order Date
2018-11    117938.16
2017-12     95739.12
2018-09     86152.89
2018-12     83030.39
2015-09     81623.53
Freq: M, Name: Sales, dtype: float64
```

```python
monthly.nsmallest(5)
```

```
Order Date
2015-02     4519.89
2016-02    11951.41
2015-01    14205.71
2016-01    18066.96
2017-01    18542.49
Freq: M, Name: Sales, dtype: float64
```

`nlargest(5)` and `nsmallest(5)` are shorter than sorting and then taking `head()`.

Every one of the worst five months is a January or February. February 2015 made $4,519, which is 4% of November 2018. The start of the year is consistently dead for this business.

---

## Part 2: Which products make the money?

### Question: How do the three categories really compare?

In Class 1 we found Technology earns most. Now let us see everything at once.

```python
df.groupby("Category").agg(
    total_sales=("Sales", "sum"),
    lines=("Sales", "count"),
    avg_sale=("Sales", "mean"),
    orders=("Order ID", "nunique"),
    products=("Product Name", "nunique")
).round(2)
```

```
                 total_sales  lines  avg_sale  orders  products
Category
Furniture          728658.58   2078    350.65    1727       380
Office Supplies    705422.33   5909    119.38    3676      1057
Technology         827455.87   1813    456.40    1519       412
```

This is **named aggregation**. Instead of `.agg(["sum","mean"])` we write `new_name=("column", "function")`. It lets us mix different columns in one result, which the old way cannot do.

The full picture in one table:

- Technology sells through only 1,519 orders but earns the most, because each sale averages $456
- Office Supplies needs 3,676 orders and 1,057 different products to earn the least
- Furniture sits between the two

Office Supplies is the hardest working category and the weakest earner.

### Question: Inside each category, which sub-category matters?

```python
df.groupby(["Category", "Sub-Category"])["Sales"].agg(["sum", "count", "mean"]).round(2)
```

```
                                    sum  count     mean
Category        Sub-Category
Furniture       Bookcases     113813.20    226   503.60
                Chairs        322822.73    607   531.83
                Furnishings    89212.02    931    95.82
                Tables        202810.63    314   645.89
Office Supplies Appliances    104618.40    459   227.93
                Art            26705.41    785    34.02
                Binders       200028.78   1492   134.07
                Envelopes      16128.05    248    65.03
                Fasteners       3001.96    214    14.03
                Labels         12347.73    357    34.59
                Paper          76828.30   1338    57.42
                Storage       219343.39    832   263.63
                Supplies       46420.31    184   252.28
Technology      Accessories   164186.70    756   217.18
                Copiers       146248.09     66  2215.88
                Machines      189238.63    115  1645.55
                Phones        327782.45    876   374.18
```

Passing a **list** of two columns to `groupby` creates groups inside groups. The result has two levels on the left.

Notice the strange pair inside Furniture. Furnishings has 931 rows, the most in the category, but earns only $89,212. Chairs has 607 rows and earns $322,822. Furnishings are cheap items that keep the shelf busy without earning much.

### Question: What share of its own category does each sub-category hold?

Raw totals are hard to compare across categories of different size. Percentages are easier.

```python
sub = df.groupby(["Category", "Sub-Category"])["Sales"].sum()
(sub / sub.groupby(level=0).transform("sum") * 100).round(1)
```

```
Category         Sub-Category
Furniture        Bookcases       15.6
                 Chairs          44.3
                 Furnishings     12.2
                 Tables          27.8
Office Supplies  Appliances      14.8
                 Art              3.8
                 Binders         28.4
                 Envelopes        2.3
                 Fasteners        0.4
                 Labels           1.8
                 Paper           10.9
                 Storage         31.1
                 Supplies         6.6
Technology       Accessories     19.8
                 Copiers         17.7
                 Machines        22.9
                 Phones          39.6
Name: Sales, dtype: float64
```

`transform("sum")` is the new idea here. Normal `groupby().sum()` gives one row per group. `transform("sum")` gives the group total **repeated next to every original row**, so we can divide each value by its own group total.

`level=0` means group by the first level of the index, which is Category.

Now each category has a clear leader:

- Furniture depends on Chairs (44.3%)
- Office Supplies depends on Storage and Binders (31.1% and 28.4%)
- Technology depends on Phones (39.6%)

And Fasteners is 0.4% of its category. If the store stopped selling Fasteners tomorrow, almost nothing would change.

### Question: Which sub-categories are growing and which are dying?

Totals hide direction. Compare the first year with the last year.

```python
pv = df.pivot_table(index="Sub-Category", columns="Year", values="Sales", aggfunc="sum").round(2)
pv["growth%"] = ((pv[2018] - pv[2015]) / pv[2015] * 100).round(1)
pv.sort_values("growth%", ascending=False)
```

```
Year              2015      2016      2017       2018  growth%
Sub-Category
Copiers       10849.78  26179.45  46319.47   62899.39    479.7
Appliances    15160.72  23228.18  26016.79   40212.72    165.2
Accessories   23766.40  40443.97  41735.36   58240.97    145.1
Furnishings   13636.99  20525.22  26845.12   28204.69    106.8
Paper         14332.72  14664.76  20326.04   27504.78     91.9
Binders       43263.27  36049.75  48994.52   71721.26     65.8
Bookcases     20036.68  37476.77  26275.47   30024.28     49.8
Art            5897.53   6091.64   5890.61    8825.63     49.6
Storage       49197.53  43321.21  58751.96   68072.70     38.4
Labels         2825.46   2884.91   2792.66    3844.70     36.1
Phones        77225.96  67870.61  78000.22  104685.66     35.6
Tables        45757.78  35397.20  60762.11   60893.54     33.1
Fasteners       655.39    545.22    946.27     855.07     30.5
Chairs        77046.44  70654.67  81930.34   93191.27     21.0
Supplies      14335.62   1890.52  14242.06   15952.11     11.3
Envelopes      3844.59   4448.22   4456.66    3378.57    -12.1
Machines      62023.37  27763.70  55906.89   43544.68    -29.8
```

We created a new column on a pivot table, exactly the way we create a column on a DataFrame. `pv[2018]` works because the column names here are numbers, not text.

This is the most useful table so far:

- **Copiers grew 480%.** From $10,849 to $62,899 in four years. This is the story of the whole dataset.
- **Machines fell 30%.** They also crashed in 2016 ($62,023 down to $27,763) and never fully recovered.
- **Envelopes** is the only other loser.

Now go back to our first question. 2016 was the bad year. Look at the 2016 column: Machines lost about $34,000 and Chairs, Phones, Binders and Tables all dropped too. The dip was not one product going wrong, it was several at once.

---

## Part 3: Where does the money come from?

### Question: How do the four regions compare?

```python
df.groupby("Region").agg(
    total_sales=("Sales", "sum"),
    orders=("Order ID", "nunique"),
    customers=("Customer ID", "nunique")
).round(2).sort_values("total_sales", ascending=False)
```

```
         total_sales  orders  customers
Region
West        710219.68    1587        681
East        669518.73    1369        669
Central     492646.91    1156        626
South       389151.46     810        509
```

South is not weak because its customers are bad. It has 509 customers but only 810 orders, which is 1.6 orders per customer. West has 681 customers and 1,587 orders, which is 2.3 each. South has both fewer customers and less repeat buying.

### Question: Are the regions changing over time?

```python
df.pivot_table(index="Region", columns="Year", values="Sales", aggfunc="sum").round(2)
```

```
Year          2015       2016       2017       2018
Region
Central  102920.52  102425.17  145673.88  141627.34
East     127652.82  153225.18  178511.54  210129.19
South    103374.91   70076.08   93535.90  122164.57
West     145907.96  133709.57  182471.23  248130.93
```

East grows every single year without a break. West had the biggest jump in 2018. South collapsed in 2016 ($103,374 down to $70,076) and only passed its 2015 level in 2018. Central has been flat since 2017.

So the 2016 dip was mostly a **South region problem**.

### Question: Does each region sell the same mix of products?

```python
region_cat = df.pivot_table(index="Category", columns="Region", values="Sales", aggfunc="sum")
(region_cat / region_cat.sum() * 100).round(1)
```

```
Region           Central  East  South  West
Category
Furniture           32.5  30.8   29.9  34.5
Office Supplies     33.2  29.9   32.0  30.6
Technology          34.3  39.3   38.1  34.8
```

`region_cat.sum()` adds each column, and dividing by it gives each category's share **within its own region**.

The mixes are close, but East is the most Technology heavy (39.3%) and West is the most Furniture heavy (34.5%). If you were planning stock for a warehouse, this small difference matters.

### Question: Which states carry each region?

```python
state_sales = df.groupby(["Region", "State"])["Sales"].sum().round(2)
state_sales.groupby(level=0, group_keys=False).nlargest(3)
```

```
Region   State
Central  Texas             168572.53
         Illinois           79236.52
         Michigan           76136.07
East     New York          306361.15
         Pennsylvania      116276.65
         Ohio               75130.35
South    Florida            88436.53
         Virginia           70636.72
         North Carolina     55165.96
West     California        446306.46
         Washington        135206.85
         Arizona            35272.66
```

This gives the top 3 states inside every region. `group_keys=False` stops Pandas from adding a duplicate Region level to the output.

California is $446,306 while the second West state, Washington, is $135,206. One state is carrying the strongest region. New York does the same job for East. That is a business risk worth reporting: lose California and the West region collapses.

### Question: Are big states big because of volume or because of price?

```python
states = df.groupby("State").agg(
    sales=("Sales", "sum"),
    lines=("Sales", "count")
).round(2)
states["avg_sale"] = (states["sales"] / states["lines"]).round(2)
states.sort_values("sales", ascending=False).head(10)
```

```
                  sales  lines     avg
State
California    446306.46   1946  229.35
New York      306361.15   1097  279.27
Texas         168572.53    973  173.25
Washington    135206.85    504  268.27
Pennsylvania  116276.65    582  199.79
Florida        88436.53    373  237.10
Illinois       79236.52    483  164.05
Michigan       76136.07    253  300.93
Ohio           75130.35    454  165.49
Virginia       70636.72    224  315.34
```

California is big because of **volume** (1,946 rows at an average $229). Virginia is different: only 224 rows but $315 average, the highest in the top ten. Texas is the opposite, 973 rows at only $173 each.

```python
round(states["sales"].nlargest(5).sum() / df["Sales"].sum() * 100, 1)
```

```
51.9
```

Five states out of 49 produce 51.9% of all revenue.

### Question: What about the weakest states?

```python
states.sort_values("sales").head(5)
```

```
                 sales  lines      avg
State
North Dakota    919.91      7   131.42
West Virginia  1209.82      4   302.46
Maine          1270.53      8   158.82
South Dakota   1315.56     12   109.63
Wyoming        1603.14      1  1603.14
```

Look at Wyoming. Total sales $1,603 from exactly **one row**. The state appears in this dataset because of a single purchase. Any "average sales per state" calculation that includes Wyoming is misleading. Always check the count before you trust an average.

### Question: How concentrated are the cities?

```python
cities = df.groupby("City").agg(
    sales=("Sales", "sum"),
    lines=("Sales", "count")
).round(2)
cities["avg_sale"] = (cities["sales"] / cities["lines"]).round(2)
cities.sort_values("sales", ascending=False).head(10)
```

```
                   sales  lines     avg
City
New York City  252462.55    891  283.35
Los Angeles    173420.18    728  238.21
Seattle        116106.32    426  272.55
San Francisco  109041.12    500  218.08
Philadelphia   108841.75    532  204.59
Houston         63956.14    374  171.01
Chicago         47820.13    308  155.26
San Diego       47521.03    170  279.54
Jacksonville    44713.18    125  357.71
Detroit         42446.94    115  369.10
```

```python
round(cities["sales"].nlargest(10).sum() / df["Sales"].sum() * 100, 1)
```

```
44.5
```

```python
(cities["sales"] < 1000).sum(), len(cities)
```

```
(277, 529)
```

10 cities out of 529 give 44.5% of revenue. At the same time, **277 cities have made less than $1,000 in four years**. More than half the cities in this dataset are almost nothing to the business.

Jacksonville and Detroit are interesting. They are not in the top five by money, but their average sale ($357 and $369) is the highest on this list. Few orders, big ones.

---

## Part 4: Who is buying?

### Question: Which customer segment is the strongest?

```python
df.groupby("Segment").agg(
    customers=("Customer ID", "nunique"),
    orders=("Order ID", "nunique"),
    sales=("Sales", "sum"),
    avg_line=("Sales", "mean")
).round(2)
```

```
             customers  orders       sales  avg_line
Segment
Consumer           409    2537  1148060.53    225.07
Corporate          236    1491   688494.07    233.15
Home Office        148     894   424982.18    243.40
```

Consumer earns the most simply because it has the most customers. But look at `avg_line`. Home Office has the **highest** average sale ($243) with the fewest customers.

Let us make that comparison fair by going per customer:

```python
seg = df.groupby("Segment").agg(
    customers=("Customer ID", "nunique"),
    sales=("Sales", "sum")
)
(seg["sales"] / seg["customers"]).round(2)
```

```
Segment
Consumer       2806.99
Corporate      2917.35
Home Office    2871.50
Name: sales, dtype: float64
```

All three segments are worth almost exactly the same per customer, between $2,807 and $2,917. Consumer is not a better segment, it is only a bigger one. To grow Corporate or Home Office, the store needs more customers, not better ones.

### Question: Are segments growing at the same speed?

```python
df.pivot_table(index="Segment", columns="Year", values="Sales", aggfunc="sum").round(2)
```

```
Year              2015       2016       2017       2018
Segment
Consumer     262956.80  265356.29  291142.97  328604.47
Corporate    127797.50  119675.60  204977.32  236043.66
Home Office   89101.91   74404.11  104072.27  157403.88
```

Home Office grew from $89,101 to $157,403, which is 77%. Consumer grew only 25%. The smallest segment is growing the fastest.

### Question: Who are the biggest customers, and are they really loyal?

```python
customers = df.groupby("Customer Name").agg(
    total=("Sales", "sum"),
    orders=("Order ID", "nunique"),
    lines=("Sales", "count")
).round(2)
customers["avg_order"] = (customers["total"] / customers["orders"]).round(2)
customers.sort_values("total", ascending=False).head(10)
```

```
                       total  orders  lines  avg_order
Customer Name
Sean Miller         25043.05       5     15    5008.61
Tamara Chand        19052.22       5     12    3810.44
Raymond Buch        15117.34       6     18    2519.56
Tom Ashbrook        14595.62       4     10    3648.90
Adrian Barton       14473.57      10     20    1447.36
Ken Lonsdale        14175.23      12     29    1181.27
Sanjit Chand        14142.33       9     22    1571.37
Hunter Lopez        12873.30       6     11    2145.55
Sanjit Engle        12209.44      11     19    1109.95
Christopher Conant  12129.07       5     11    2425.81
```

Now compare two customers on this list:

- **Sean Miller**: $25,043 from 5 orders. We know from Class 1 that $22,638 of that came from one Cisco machine.
- **Ken Lonsdale**: $14,175 from 12 orders, spread across 29 product lines.

Sean Miller looks better in a ranking. Ken Lonsdale is better for the business, because his money arrives regularly. A single ranking column hides this, which is why we added `orders` and `avg_order` to the table.

### Question: Does a small group of customers carry the business?

```python
total_sales = df["Sales"].sum()
top20 = customers["total"].nlargest(20).sum()
round(top20 / total_sales * 100, 1)
```

```
11.7
```

```python
top79 = customers["total"].nlargest(79).sum()   # 79 is 10% of 793 customers
round(top79 / total_sales * 100, 1)
```

```
30.9
```

The top 10% of customers bring 30.9% of the money. That is concentrated, but not extreme. This store is not living off ten rich clients.

```python
orders_per_customer = df.groupby("Customer ID")["Order ID"].nunique()
orders_per_customer.describe().round(2)
```

```
count    793.00
mean       6.21
std        2.53
min        1.00
50%        6.00
max       17.00
Name: Order ID, dtype: float64
```

```python
(orders_per_customer == 1).sum()
```

```
13
```

Only 13 customers out of 793 ordered once and never came back. The median customer ordered 6 times. This is a business with genuinely loyal customers.

---

## Part 5: Product level analysis

### Question: What are the top 10 products?

```python
products = df.groupby("Product Name").agg(
    total=("Sales", "sum"),
    times=("Sales", "count")
).round(2)
products.sort_values("total", ascending=False).head(10)
```

```
                                                        total  times
Product Name
Canon imageCLASS 2200 Advanced Copier                61599.82      5
Fellowes PB500 Electric Punch Plastic Comb Bind...   27453.38     10
Cisco TelePresence System EX90 Videoconferencin...   22638.48      1
HON 5400 Series Task Chairs for Big and Tall         21870.58      8
GBC DocuBind TL300 Electric Binding System           19823.48     11
GBC Ibimaster 500 Manual ProClick Binding System     19024.50      9
Hewlett Packard LaserJet 3310 Copier                 18839.69      8
HP Designjet T520 Inkjet Large Format Printer -...   18374.90      3
GBC DocuBind P400 Electric Binding System            17965.07      6
High Speed Automatic Electric Letter Opener          17030.31      3
```

Adding the `times` column changes the story. The Canon copier earned $61,599 from only **5 sales**, about $12,300 each. The Cisco unit was sold **once**.

The top ten products are not popular products. Every one of them was sold 11 times or fewer in four years, and they are all expensive machines or chairs. If two or three of these sales had not happened, the yearly numbers would look very different.

### Question: What are the bottom 10 products?

```python
products.sort_values("total").head(10)
```

```
                                                     total  times
Product Name
Eureka Disposable Bags for Sanitaire Vibra Groomer     1.62      1
Avery 5                                                5.76      1
Xerox 20                                               6.48      1
Grip Seal Envelopes                                    7.07      1
Acme Serrated Blade Letter Opener                      7.63      1
Avery Hi-Liter Pen Style Six-Color Fluorescent Set      7.70      1
Avery Hi-Liter Comfort Grip Fluorescent Highlighter     7.80      2
Xerox 1989                                             7.97      1
4009 Highlighters                                      8.04      1
Stockwell Gold Paper Clips                             8.10      2
```

The worst product earned $1.62 in four years.

### Question: How much of the catalogue is actually useless?

```python
len(products)
```

```
1849
```

```python
(products["times"] == 1).sum()
```

```
94
```

```python
bottom_half = products["total"].sort_values().head(len(products) // 2).sum()
round(bottom_half / total_sales * 100, 1)
```

```
4.8
```

```python
round(products["total"].nlargest(20).sum() / total_sales * 100, 1)
```

```
17.2
```

This is the strongest finding of the whole analysis:

- 1,849 different products exist
- 94 were sold exactly once in four years
- The weakest 924 products together produced **4.8%** of revenue
- The strongest 20 products produced **17.2%**

Twenty products beat nine hundred products, three times over. The catalogue is far too wide at the bottom.

### Question: What does a typical order look like?

Until now every calculation used product rows. Let us group by order instead.

```python
order_value = df.groupby("Order ID")["Sales"].sum()
order_value.describe().round(2)
```

```
count     4922.00
mean       459.48
std        958.67
min          0.56
25%         37.27
50%        151.88
75%        513.51
max      23661.23
Name: Sales, dtype: float64
```

The average order is $459 but the median order is only $151. Same pattern as Class 1: a few very large orders pull the average up.

```python
(order_value > 5000).sum()
```

```
27
```

```python
round(order_value[order_value > 5000].sum() / total_sales * 100, 1)
```

```
10.5
```

**27 orders out of 4,922 produced 10.5% of all revenue.** That is one half of one percent of the orders. This is the same lesson as the products: this business depends heavily on rare, large transactions.

---

## Part 6: Shipping

We have four shipping columns we have not used: `Ship Mode`, `Ship Date`, and our own `Ship Days`.

### Question: Do the shipping modes actually deliver at different speeds?

```python
df.groupby("Ship Mode")["Ship Days"].agg(["mean", "min", "max", "count"]).round(2)
```

```
                mean  min  max  count
Ship Mode
First Class     2.18    1    4   1501
Same Day        0.04    0    1    538
Second Class    3.25    1    5   1902
Standard Class  5.01    3    7   5859
```

The names are honest. Same Day averages 0.04 days, First Class 2.18, Second Class 3.25, Standard Class 5.01. There is no overlap problem: Standard Class never ships faster than 3 days, and First Class never takes more than 4.

```python
df["Ship Days"].value_counts().sort_index()
```

```
Ship Days
0     514
1     363
2    1295
3     978
4    2718
5    2147
6    1170
7     615
Name: count, dtype: int64
```

Nothing takes longer than 7 days and nothing is negative. The shipping data is clean.

### Question: Do customers spend more when they pay for faster shipping?

```python
df.groupby("Ship Mode")["Sales"].agg(["sum", "mean", "count"]).round(2)
```

```
                       sum    mean  count
Ship Mode
First Class      345572.26  230.23   1501
Same Day         125219.04  232.75    538
Second Class     449914.18  236.55   1902
Standard Class  1340831.31  228.85   5859
```

This is a genuinely surprising result. The average sale is between $228 and $237 for **all four modes**. Speed of shipping has no relationship with the size of the sale.

Standard Class dominates the totals ($1.34 million) only because 5,859 of the 9,800 rows use it. Nearly 60% of everything ships the slow way.

### Question: Do business customers choose faster shipping?

```python
df.pivot_table(index="Segment", columns="Ship Mode", values="Sales", aggfunc="count")
```

```
Ship Mode    First Class  Same Day  Second Class  Standard Class
Segment
Consumer             755       312          1003            3031
Corporate            468       114           589            1782
Home Office          278       112           310            1046
```

Using `aggfunc="count"` counts rows instead of adding money.

Convert to percentages within each segment to compare fairly:

```python
seg_ship = df.pivot_table(index="Segment", columns="Ship Mode", values="Sales", aggfunc="count")
(seg_ship.div(seg_ship.sum(axis=1), axis=0) * 100).round(1)
```

```
Ship Mode    First Class  Same Day  Second Class  Standard Class
Segment
Consumer            14.8       6.1          19.7            59.4
Corporate           15.8       3.9          19.9            60.3
Home Office         15.9       6.4          17.8            59.9
```

`div(..., axis=0)` divides each row by that row's total. `axis=1` inside `sum()` means add across the columns.

All three segments behave almost identically. Around 60% choose Standard Class no matter who they are. There is no "business customers are in a hurry" effect in this data.

### Question: Is any region shipped slower than the others?

```python
df.pivot_table(index="Region", columns="Ship Mode", values="Ship Days", aggfunc="mean").round(2)
```

```
Ship Mode  First Class  Same Day  Second Class  Standard Class
Region
Central           2.31      0.03          3.36            4.98
East              2.21      0.00          3.27            4.97
South             2.12      0.01          3.20            5.01
West              2.10      0.10          3.17            5.07
```

Every region gets the same service. The biggest gap anywhere is about 0.2 days. Whatever is causing the South region to underperform, it is not delivery speed.

---

## Part 7: Strange things worth reporting

Good EDA means also reporting what looks wrong or unexpected.

**1. One product carried a whole sub-category.**

```python
df[df["Sub-Category"] == "Copiers"]["Sales"].nlargest(5)
```

```
6826    17499.950
8153    13999.960
2623    11199.968
4190    10499.970
6425     8399.976
Name: Sales, dtype: float64
```

Copiers earned $146,248 from only 66 sales. Those five sales alone are $61,599 of it, and all five are the same product, the Canon imageCLASS 2200. One product is 42% of the sub-category.

**2. Machines is the only sub-category that is clearly dying.** It fell from $62,023 in 2015 to $43,544 in 2018, with a crash to $27,763 in 2016.

**3. Wyoming exists in this dataset because of one purchase.** One row, $1,603.

**4. Furnishings is busy but poor.** 931 rows, more than Chairs, but $89,212 against Chairs' $322,822.

**5. February is the worst month in three years out of four.**

```python
df.pivot_table(index="Month", columns="Year", values="Sales", aggfunc="sum").round(2)
```

```
Year       2015      2016      2017       2018
Month
1      14205.71  18066.96  18542.49   43476.47
2       4519.89  11951.41  22978.82   19921.00
3      55205.80  32339.32  51165.06   58863.41
...
11     77907.66  75249.40  79066.50  117938.16
12     68167.06  74543.60  95739.12   83030.39
```

```python
df.pivot_table(index="Month", columns="Year", values="Sales", aggfunc="sum").idxmin()
```

```
Year
2015    2
2016    2
2017    1
2018    2
dtype: int64
```

`idxmin()` gives the row label of the smallest value in each column, so it tells us the worst month of each year without reading the table by eye. February wins three times and January once. February 2015 was $4,519, which is under 4% of November 2018.

**6. Supplies collapsed in 2016 and recovered.** It went $14,335 to $1,890 and back to $14,242. A drop of 87% in one year and a full recovery the next is unusual. This is worth asking the business about, because it may be a data problem rather than a real event.

---
