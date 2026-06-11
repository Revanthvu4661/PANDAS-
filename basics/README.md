# 📘 Pandas Basics

Everything you need to start working with data using Pandas — from loading a file to filtering, grouping, and saving results.

---

## 📌 Table of Contents

- [What is Pandas?](#what-is-pandas)
- [Installation & Import](#installation--import)
- [Two Main Types](#two-main-types)
- [Loading Data](#loading-data)
- [Exploring Data](#exploring-data)
- [Selecting Data](#selecting-data)
- [Filtering Rows](#filtering-rows)
- [Sorting](#sorting)
- [GroupBy & Aggregation](#groupby--aggregation)
- [Merging & Joining](#merging--joining)
- [Saving Output](#saving-output)
- [Cheat Sheet](#cheat-sheet)

---

## What is Pandas?

Pandas is a Python library that lets you work with data in a structured way — like a spreadsheet, but inside Python and much more powerful.

**Think of it like this:**
- Excel → you click buttons to filter and sort
- Pandas → you write code to do the same things, but 100x faster on large data

**What Pandas can do:**
- Load data from CSV, Excel, SQL, JSON
- Clean messy data (missing values, wrong types, duplicates)
- Filter, sort, group, and summarize data
- Merge multiple datasets together
- Prepare data for machine learning

---

## Installation & Import

```python
# Install Pandas (run once in terminal)
pip install pandas
```

```python
# Import at the top of every Python file
import pandas as pd
```

> `pd` is just a short nickname — you could write `pandas` instead, but everyone uses `pd` by convention.

---

## Two Main Types

Pandas has two core data types you'll use constantly:

| Type | Shape | Think of it as |
|------|-------|----------------|
| `DataFrame` | 2-D (rows + columns) | A full spreadsheet / table |
| `Series` | 1-D (a single list) | One column or one row |

```python
# A DataFrame — 2D table
df = pd.read_csv("students.csv")

# A Series — one column from that table
names = df["Name"]   # this is a Series
```

---

## Loading Data

```python
import pandas as pd

# Load a CSV file
df = pd.read_csv("file.csv")

# Load an Excel file
df = pd.read_excel("file.xlsx")

# Load a JSON file
df = pd.read_json("file.json")

# Load from SQL database
df = pd.read_sql("SELECT * FROM table_name", connection)

# Print the DataFrame
print(df)
```

> **What is a CSV?**  
> CSV = Comma Separated Values. It's a plain text file where data is separated by commas. Excel files end in `.xlsx`, CSVs end in `.csv`.

---

## Exploring Data

After loading data, always explore it first to understand what you're working with.

```python
df.head()        # shows first 5 rows
df.head(10)      # shows first 10 rows

df.tail()        # shows last 5 rows

df.info()        # column names, data types, how many non-null values

df.describe()    # statistics — mean, min, max, std for numeric columns

df.shape         # returns (number of rows, number of columns)
                 # e.g. (1000, 8) means 1000 rows and 8 columns

df.columns       # list of all column names

df.index         # row index (usually 0, 1, 2, 3 ...)

df.dtypes        # data type of each column (int, float, object, etc.)
```

---

## Selecting Data

### Pick columns

```python
# Single column → returns a Series
df["Name"]

# Multiple columns → returns a DataFrame
df[["Name", "Age", "Score"]]

# Count rows in a column
len(df["Name"])
```

### Unique values in a column

```python
df["City"].unique()          # shows all unique values
df["City"].nunique()         # count of unique values
df["City"].value_counts()    # how many times each value appears
```

### Select by position — `iloc`

> `iloc` = **Index Location** (uses numbers, like array indexing)

```python
df.iloc[0]            # first row (index 0)
df.iloc[5]            # 6th row
df.iloc[-1]           # last row

list(df.iloc[0])      # first row values as a list

df.iloc[0]["Name"]    # first row → get the "Name" column value

df.iloc[0:5]          # rows 0 to 4 (5 rows)
df.iloc[0:5, 0:3]     # rows 0-4, columns 0-2
```

### Select by label — `loc`

> `loc` = **Label Location** (uses column names and row labels)

```python
df.loc[0]                           # row with label/index 0
df.loc[0:5, "Name"]                 # rows 0–5, only Name column
df.loc[0:5, ["Name", "Age"]]        # rows 0–5, two columns
df.loc[0, "Name"] = "New Value"     # update a specific cell
```

> **iloc vs loc:**
> - `iloc[5]` → 5th position (always a number)
> - `loc[5]` → row where index label is 5 (could be different if index was reset)

---

## Filtering Rows

Filtering means keeping only the rows that match a condition.

### Single condition

```python
# Keep only rows where Category is "Electronics"
df[df["Category"] == "Electronics"]

# Keep rows where Age is 18 or above
df[df["Age"] >= 18]

# Keep rows where Sales is less than 500
df[df["Sales"] < 500]
```

### Multiple conditions

```python
# AND → both conditions must be true  (use & not 'and')
df[(df["Category"] == "Electronics") & (df["Country"] == "India")]

# OR → at least one condition must be true  (use | not 'or')
df[(df["Category"] == "Electronics") | (df["Country"] == "India")]
```

> ⚠️ Always wrap each condition in `()` when combining with `&` or `|`

### Range filter

```python
df[df["Price"].between(100, 500)]     # Price between 100 and 500
```

### String filters

```python
df[df["Name"].str.startswith("A")]              # names starting with A
df[df["Name"].str.endswith("i")]                # names ending with i
df[df["Name"].str.contains("kumar", case=False)]  # contains "kumar" (case insensitive)
```

### Filter from a list — `isin`

```python
# Keep only rows where City is one of these
df[df["City"].isin(["Hyderabad", "Guntur", "Vijayawada"])]

# Opposite — exclude these cities
df[~df["City"].isin(["Hyderabad", "Guntur"])]
```

---

## Sorting

```python
# Sort by one column — ascending (A to Z, small to large)
df.sort_values("Name")

# Sort descending (Z to A, large to small)
df.sort_values("Sales", ascending=False)

# Sort by multiple columns
df.sort_values(["Category", "Sales"], ascending=[True, False])

# Sort by row index
df.sort_index()
```

---

## GroupBy & Aggregation

GroupBy is like asking: *"For each category, what is the total sales?"*  
It groups rows together and then runs a calculation on each group.

```python
# Total sales per category
df.groupby("Category")["Sales"].sum()

# Average sales per category
df.groupby("Category")["Sales"].mean()

# Count rows per category
df.groupby("Category")["Sales"].count()

# Max and Min
df.groupby("Category")["Sales"].max()
df.groupby("Category")["Sales"].min()

# Multiple calculations at once
df.groupby("Category").agg({
    "Sales": "sum",
    "Quantity": "mean",
    "Price": "max"
})

# Group by multiple columns
df.groupby(["Category", "Region"])["Sales"].sum()
```

> **SQL equivalent:**  
> `SELECT Category, SUM(Sales) FROM table GROUP BY Category`  
> becomes → `df.groupby("Category")["Sales"].sum()`

---

## Merging & Joining

Merging combines two DataFrames based on a common column — exactly like SQL JOINs.

```python
# INNER JOIN — only rows that exist in BOTH tables
merged = pd.merge(df1, df2, on="ID", how="inner")

# LEFT JOIN — all rows from df1, matching rows from df2
merged = pd.merge(df1, df2, on="ID", how="left")

# RIGHT JOIN — all rows from df2, matching rows from df1
merged = pd.merge(df1, df2, on="ID", how="right")

# FULL OUTER JOIN — all rows from both tables
merged = pd.merge(df1, df2, on="ID", how="outer")

# Stack DataFrames vertically (same columns)
combined = pd.concat([df1, df2])

# Stack DataFrames side by side (same rows)
combined = pd.concat([df1, df2], axis=1)
```

---

## Saving Output

```python
# Save as CSV (most common)
df.to_csv("output.csv", index=False)

# Save as Excel
df.to_excel("output.xlsx", index=False)

# Save as JSON
df.to_json("output.json")
```

> `index=False` means don't write the row numbers (0, 1, 2...) into the file.

---

## Cheat Sheet

| Task | Code |
|------|------|
| Load CSV | `pd.read_csv("file.csv")` |
| First 5 rows | `df.head()` |
| Shape | `df.shape` |
| Column names | `df.columns` |
| Data types | `df.dtypes` |
| Select column | `df["col"]` |
| Select multiple | `df[["c1", "c2"]]` |
| By position | `df.iloc[n]` |
| By label | `df.loc[n]` |
| Filter | `df[df["col"] == "val"]` |
| AND filter | `df[(cond1) & (cond2)]` |
| OR filter | `df[(cond1) \| (cond2)]` |
| isin | `df["col"].isin([...])` |
| String filter | `df["col"].str.contains("x")` |
| Sort | `df.sort_values("col")` |
| GroupBy | `df.groupby("col")["val"].sum()` |
| Merge | `pd.merge(df1, df2, on="key")` |
| Save CSV | `df.to_csv("out.csv", index=False)` |

---

➡️ Next: [Data Cleaning →](../data-cleaning/README.md)

---

> Made with 🐼 by **Revanth** | VFSTR CSE
