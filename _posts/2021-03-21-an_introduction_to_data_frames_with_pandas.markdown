---
layout: post
title:      "An introduction to data frames with Pandas"
date:       2021-03-21 20:10:11 +0000
permalink:  an_introduction_to_data_frames_with_pandas
---


Pandas is a python module commonly used for data analysis, similar to R in its use of the data frame structure. Its uses are not just limited to data science. As a developer there are situations where you might use it to store or look up data instead of a SQL database, especially when you are importing and exporting data from a different format, such as Excel or CSV.

# Importing pandas

In order to use pandas in your python script, first you need to import it as follows:

```
import numpy as np
import pandas as pd
```

Note that pandas is closely integrated with the NumPy modules which provides support for arrays and natrices in addition to mathematical operations

# Data structures

One basic data structure in Pandas is the linear Series

```
s = pd.Series([1, 3, 5, np.nan, 6, 8])
```

All these values will be indexed starting from zero. Note the values np.nan to denote a missing element. This is the "Not a Number" (NaN) value imported from the NumPy modules

A two-dimensional DataFrame can be created in a few ways, the first creates a matrix array with the help of NumPy:

```
dates = pd.date_range("20130101", periods=6)
df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list("ABCD"))
```

The list of dates is used as an index (the key on the left side of the table), the values are random numbers, and ABCD at the top labels the columns.
Another way to create a DataFrame uses a hash, where the keys are the column names and the values are series-like objects:

```
df2 = pd.DataFrame(
  {
    "A": 1.0,
    "B": pd.Timestamp("20130102"),
    "C": pd.Series(1, index=list(range(4)), dtype="float32"),
    "D": np.array([3] * 4, dtype="int32"),
    "E": pd.Categorical(["test", "train", "test", "train"]),
    "F": "foo",
  }
)
```

Each column will have a different datatype, and this is allowed in pandas DataFrames.

# How to view data

The following commands display the top five rows, and the bottom three rows:

```
df.head()
df.tail(3)
```

You can also view just the index, or just the column names:

```
df.index
df.columns
```

The describe function df.describe() gives a statistical summary by quartile, including the mean, standard deviation, min and max values, and quartiles.

You can also sort the values in a certain column in ascending order

```
df.sort_values(by="B")
```

# Selection

This operation to get a value is familiar if you have experience with SQL databases, but how it works in pandas is much different. You can use standard python syntax (i.e. retrieving from a list by index). You can select a single column by name, with df["A"] or get specific rows by index number or name:

```
df[0:3]
df["20130102":"20130104]
```

But there are also the pandas-specific methods like .iloc or .loc (.at and.iat are two more).
The .loc method allows you to retrieve a single column or row, or multiple of these:

```
df.loc[dates[0]]
A    0.469112
B   -0.282863
C   -1.509059
D   -1.135632
Name: 2013-01-01 00:00:00, dtype: float64
```

```
df.loc[:, ["A", "B"]]

                   A         B
2013-01-01  0.469112 -0.282863
2013-01-02  1.212112 -0.173215
2013-01-03 -0.861849 -2.104569
2013-01-04  0.721555 -0.706771
2013-01-05 -0.424972  0.567020
2013-01-06 -0.673690  0.113648
```

if you want a single value you can put both the column and row into .loc (or .at which works the same way)

```
df.loc[dates[0], "A"]
df.at[dates[0], "A"]
0.4691122999071863
```

The .iloc method retrieves the value at the given position. This is useful if your data frame is already indexed in a different way (like, you have a section that begins at 156 instead of 0), but you don't want to use those indices:

```
df.iloc[3]
A    0.721555
B   -0.706771
C   -1.039575
D    0.271860
Name: 2013-01-04 00:00:00, dtype: float64
```

```
df.iloc[1, 1]

```

.iat is equivalent to the above to retrieve a single value

# Reading and writing data

Pandas can read and write from a number of data formats, including Excel files, CSV, and HDF5 stores.
A CSV file can be imported into a dataframe with the argument of the filename:

```
pd.read_csv("foo.csv")
```

An Excel file can be similarly imported. Here, the arguments specify not only the filename, but also the sheet name (an Excel workbook may have several), the column of values to use as the index for pandas, and the value to use for blank entires (na_values)

```
pd.read_excel("foo.xlsx", "Sheet1", index_col=None, na_values=["NA"])
```

# References

[10 minutes to pandas](https://pandas.pydata.org/docs/user_guide/10min.html)
[Input and output formats](https://pandas.pydata.org/docs/user_guide/io.htm)


The content of your blog post goes here.
