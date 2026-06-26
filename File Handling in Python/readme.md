# File Handling in Python

File handling is essential for reading and writing data to files,
enabling persistent storage. Python provides built-in functions and
methods to handle files efficiently.

> Whenever we work with files, we perform two major tasks:
>
> 1.  **Import (Reading Data)**
> 2.  **Export (Writing Data)**

Python has a built-in `open()` function to handle both operations.

------------------------------------------------------------------------

# Opening a File

Use the `open()` function to open a file. Specify the mode (`read`,
`write`, `append`, etc.).

## File Modes

  Mode   Description
  ------ ----------------------------------------------------------------
  `r`    Read (default)
  `w`    Write (Creates the file if it doesn't exist or overwrites it.)
  `a`    Append (Creates the file if needed and adds new data.)
  `x`    Exclusive creation (Fails if the file already exists.)
  `b`    Binary mode (`rb`, `wb`)
  `+`    Update mode (Read + Write)

## Example

``` python
file = open("data.txt", "r")
content = file.read()
print(content)
file.close()
```

------------------------------------------------------------------------

# Using the `with` Statement

Using `with` automatically closes the file after use.

``` python
with open("data.txt", "r") as f:
    content = f.read()
    print(content)
```

------------------------------------------------------------------------

# Example 1: Writing to a Text File

``` python
with open("output.txt", "w") as f:
    f.write("This is my first exported file.\n")
    f.write("Python makes file handling easy!")
```

------------------------------------------------------------------------

# Example 2: Appending New Data

``` python
with open("output.txt", "a") as f:
    f.write("\nNew line added successfully.")
```

------------------------------------------------------------------------

# Example 3: Exporting a List of Marks

``` python
marks = [85, 90, 78, 95]

with open("marks.txt", "w") as f:
    for m in marks:
        f.write(str(m) + "\n")
```

------------------------------------------------------------------------

# Example 4: Exporting Dictionary as JSON

``` python
import json

student = {
    "name": "Ali",
    "age": 18,
    "grade": "A"
}

with open("student.json", "w") as f:
    json.dump(student, f)
```

------------------------------------------------------------------------

# Export to CSV File

``` python
import csv

with open("students.csv", "w", newline="") as f:
    writer = csv.writer(f)

    writer.writerow(["Name", "Age", "City"])
    writer.writerow(["Ali", 18, "Lahore"])
    writer.writerow(["Sara", 19, "Karachi"])
    writer.writerow(["Bilal", 20, "Islamabad"])

print("Data exported successfully to students.csv")
```

------------------------------------------------------------------------

# Import from CSV File

``` python
import csv

with open("students.csv", "r") as f:
    reader = csv.reader(f)

    for row in reader:
        print(row)
```
