# 🧹 Data Cleaning with Pandas

Real-world data is always messy. Before you analyze anything, you need to clean it.  
This section covers every technique to turn raw, broken data into something usable.

---

## 📌 Table of Contents

- [Why Clean Data?](#why-clean-data)
- [Missing Values](#missing-values)
- [Duplicates](#duplicates)
- [Fix Data Types](#fix-data-types)
- [Rename Columns](#rename-columns)
- [Drop Rows & Columns](#drop-rows--columns)
- [Update Values](#update-values)
- [String Cleaning](#string-cleaning)
- [apply() and lambda](#apply-and-lambda)
- [Binning with cut()](#binning-with-cut)
- [DateTime Cleaning](#datetime-cleaning)
- [Cheat Sheet](#cheat-sheet)

---

## Why Clean Data?

> "Data scientists spend 80% of their time cleaning data and 20% complaining about it."

Raw data from the real world has problems like:
- Empty cells (missing values / NaN)
- The same row entered twice (duplicates)
- Numbers stored as text (`"25"` instead of `25`)
- Inconsistent formats (`"India"`, `"india"`, `"INDIA"`)
- Extra spaces (`" Hyderabad "` instead of `"Hyderabad"`)

If you don't fix these, your analysis will give wrong results.

---

## Missing Values

A missing value in Pandas is shown as `NaN` (Not a Number).

### Detect missing values

```python
df.isnull()               # True/False for every single cell
df.isnull().sum()         # count of NaN per column
df.isnull().sum().sum()   # total NaN count in entire DataFrame
df.notnull()              # opposite — True where data exists
```

### Drop missing values

```python
df.dropna()                         # drop rows that have ANY NaN
df.dropna(axis=1)                   # drop columns that have any NaN
df.dropna(subset=["Name", "Age"])   # drop rows where Name OR Age is NaN
df.dropna(thresh=3)                 # keep rows with at least 3 non-NaN values
```

### Fill missing values

```python
df.fillna(0)                                       # replace all NaN with 0
df.fillna("Unknown")                               # replace with a string

df["Age"].fillna(df["Age"].mean(), inplace=True)   # fill with average
df["Age"].fillna(df["Age"].median(), inplace=True) # fill with median
df["City"].fillna("Not Provided", inplace=True)    # fill with custom text

df.fillna(method="ffill")                          # forward fill (copy value from row above)
df.fillna(method="bfill")                          # backward fill (copy from row below)
```

> **When to drop vs fill?**
> - Drop → when very few rows have NaN and losing them is okay
> - Fill → when NaN rows are important and you can estimate the value

---

## Duplicates

A duplicate is when the same row appears more than once.

```python
df.duplicated()                          # True for every duplicate row
df.duplicated().sum()                    # count of duplicate rows

df.drop_duplicates()                     # remove all duplicate rows
df.drop_duplicates(inplace=True)         # remove without reassigning

# Check duplicates based on specific columns only
df.duplicated(subset=["Name", "Email"])

# Remove duplicates based on specific columns
df.drop_duplicates(subset=["Name", "Email"], keep="first")
# keep="first"  → keep first occurrence
# keep="last"   → keep last occurrence
# keep=False    → remove ALL occurrences
```

---

## Fix Data Types

Data types matter — if a number is stored as text, you can't do math on it.

### Check data types

```python
df.dtypes                    # shows type of each column
df["Age"].dtype              # check one column's type
```

### Convert data types

```python
df["Age"].astype(int)         # convert to integer
df["Price"].astype(float)     # convert to float
df["ID"].astype(str)          # convert to string

# Apply and save back
df["Age"] = df["Age"].astype(int)
```

### Common type issues

```python
# Number stored as string — fix it
df["Sales"] = df["Sales"].str.replace(",", "")   # remove commas first
df["Sales"] = df["Sales"].astype(float)

# Boolean stored as yes/no — convert to True/False
df["Active"] = df["Active"].map({"yes": True, "no": False})
```

---

## Rename Columns

```python
# Rename specific columns
df.rename(columns={"old_name": "new_name"}, inplace=True)
df.rename(columns={"Name": "Student_Name", "Marks": "Score"}, inplace=True)

# Rename all columns at once
df.columns = ["id", "name", "age", "city"]

# Make all column names lowercase
df.columns = df.columns.str.lower()

# Replace spaces with underscores in column names
df.columns = df.columns.str.replace(" ", "_")
```

---

## Drop Rows & Columns

```python
# Drop a row by index number
df = df.drop(0)              # drop row 0
df = df.drop([1, 3, 5])      # drop rows 1, 3, and 5

# Drop a column
df = df.drop("column_name", axis=1)
df = df.drop(["col1", "col2"], axis=1)

# Drop using inplace (no reassignment needed)
df.drop("column_name", axis=1, inplace=True)

# Reset index after dropping rows
df = df.reset_index(drop=True)
```

---

## Update Values

```python
# Update one specific cell
df.loc[0, "Name"] = "New Name"

# Update an entire column
df["City"] = "Hyderabad"

# Replace specific values across the entire DataFrame
df.replace("N/A", None)
df.replace({"Male": "M", "Female": "F"})

# Replace in one column only
df["Gender"].replace({"Male": "M", "Female": "F"}, inplace=True)

# Conditional update — if Age > 60, set as "Senior"
df.loc[df["Age"] > 60, "Category"] = "Senior"
```

---

## String Cleaning

When text data has inconsistent formatting, these methods fix it.

```python
# Remove leading/trailing spaces (very common issue)
df["Name"] = df["Name"].str.strip()

# Convert to lowercase
df["City"] = df["City"].str.lower()

# Convert to uppercase
df["City"] = df["City"].str.upper()

# Capitalize first letter only
df["Name"] = df["Name"].str.title()

# Replace part of a string
df["Phone"] = df["Phone"].str.replace("-", "")

# Extract part of a string (first 3 characters)
df["Code"] = df["Name"].str[:3]

# Split a column into two
df[["First", "Last"]] = df["Full_Name"].str.split(" ", expand=True)

# Check if string contains something
df["Name"].str.contains("kumar", case=False)

# Remove special characters using regex
df["Name"] = df["Name"].str.replace(r"[^a-zA-Z\s]", "", regex=True)
```

---

## apply() and lambda

`apply()` lets you run any custom function on every row or column.  
`lambda` is a quick one-line function.

### apply() on a column

```python
# Square every value in a column
df["Score_Squared"] = df["Score"].apply(lambda x: x ** 2)

# Custom logic
def grade(marks):
    if marks >= 90:
        return "A"
    elif marks >= 75:
        return "B"
    else:
        return "C"

df["Grade"] = df["Marks"].apply(grade)
```

### apply() on multiple columns (axis=1)

```python
# Create a new column using two existing columns
df["Full_Name"] = df.apply(lambda row: row["First"] + " " + row["Last"], axis=1)

# Total = Price × Quantity
df["Total"] = df.apply(lambda row: row["Price"] * row["Quantity"], axis=1)
```

> **When to use apply?**  
> When your logic is too complex for a simple formula and you need to process each row/value one by one.

---

## Binning with cut()

Binning means grouping numbers into categories — like converting marks into grades.

```python
# Divide Age into 3 bins
df["Age_Group"] = pd.cut(df["Age"], bins=3, labels=["Young", "Middle", "Senior"])

# Custom bin boundaries
df["Grade"] = pd.cut(
    df["Marks"],
    bins=[0, 50, 75, 90, 100],
    labels=["Fail", "Pass", "Good", "Excellent"]
)

# qcut — equal-sized bins (by frequency, not range)
df["Quartile"] = pd.qcut(df["Sales"], q=4, labels=["Q1", "Q2", "Q3", "Q4"])
```

---

## DateTime Cleaning

Dates are often stored as strings — you need to convert them to work with them properly.

```python
# Convert a column to datetime
df["Date"] = pd.to_datetime(df["Date"])

# Handle different formats
df["Date"] = pd.to_datetime(df["Date"], format="%d-%m-%Y")

# Extract parts of a date
df["Year"]  = df["Date"].dt.year
df["Month"] = df["Date"].dt.month
df["Day"]   = df["Date"].dt.day
df["Weekday"] = df["Date"].dt.day_name()    # Monday, Tuesday...

# Filter by date range
df[df["Date"] >= "2024-01-01"]
df[(df["Date"] >= "2024-01-01") & (df["Date"] <= "2024-12-31")]

# Difference between two dates
df["Days_Since"] = (pd.Timestamp.today() - df["Date"]).dt.days
```

---

## Cheat Sheet

| Task | Code |
|------|------|
| Count NaN per column | `df.isnull().sum()` |
| Drop rows with NaN | `df.dropna()` |
| Fill NaN with mean | `df["col"].fillna(df["col"].mean())` |
| Count duplicates | `df.duplicated().sum()` |
| Remove duplicates | `df.drop_duplicates()` |
| Check data types | `df.dtypes` |
| Convert to int | `df["col"].astype(int)` |
| Rename column | `df.rename(columns={"old": "new"})` |
| Drop a column | `df.drop("col", axis=1)` |
| Replace value | `df.replace("old", "new")` |
| Strip spaces | `df["col"].str.strip()` |
| Lowercase | `df["col"].str.lower()` |
| Apply function | `df["col"].apply(lambda x: x*2)` |
| Bin numbers | `pd.cut(df["col"], bins=3)` |
| Convert to datetime | `pd.to_datetime(df["col"])` |
| Extract year | `df["Date"].dt.year` |

---

⬅️ Previous: [Basics](../basics/README.md) &nbsp;&nbsp;|&nbsp;&nbsp; ➡️ Next: [Visualization →](../visualization/README.md)

---

> Made with 🐼 by **Revanth** | VFSTR CSE
