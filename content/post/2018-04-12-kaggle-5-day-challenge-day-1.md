---
title: 'Kaggle 5 Day Challenge: Day 1'
author: 
date: '2018-04-12'
slug: kaggle-5-day-challenge-day-1
categories: []
tags: []
---

# Day 3: Parsing Dates
<br>
<br>

Welcome to day 3 of the 5-Day Data Challenge!
Today, we're going to work with dates.
<br>

```{.python .input  n=104}
import pandas as pd
import numpy as np
import seaborn as sns
import datetime

# read in the data
earthquakes = pd.read_csv("earthquake_database.csv")
landslides = pd.read_csv("catalog.csv")
volcanos = pd.read_csv("volcano_database.csv")

#set seed for reproducibility
np.random.seed(0)
```


## Check the data type of our date column

For this part of the challenge, I'll
be working with the date column from the landslides dataframe. The very first
thing I'm going to do is take a look at the first couple rows to make sure it
contains dates.

```{.python .input  n=105}
# print the first few rows of the date column
earthquakes['Date'].head()
```

```{.json .output n=105}
[
 {
  "data": {
   "text/plain": "0    01/02/1965\n1    01/04/1965\n2    01/05/1965\n3    01/08/1965\n4    01/09/1965\nName: Date, dtype: object"
  },
  "execution_count": 105,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```



Those are indeed dates. Just because I recognize these are dates doesn't mean
that Python knows that they're dates. Notice that the at the bottom of the
output of head(), you can see that it says that the data type of this column is
"object".

    Pandas uses the "object" dtype for storing various types of data
types, but most often when you see a column with the dtype "object" it will have
strings in it.

If you check the pandas dtype documentation here, you'll notice
that there's also a specific datetime64 dtypes. Because the dtype of our column
is object rather than datetime64, we can tell that Python doesn't know that this
column contains dates.

We can also look at just the dtype of your column
without printing the first few rows if we like:

```{.python .input  n=106}
# check the data type of our date column
earthquakes['Date'].dtypes
```

```{.json .output n=106}
[
 {
  "data": {
   "text/plain": "dtype('O')"
  },
  "execution_count": 106,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

## Convert our date columns to datetime
<br>
Now that we know python isn't
recognizing our date column as a date, it's time to convert it so that it is
recognized. This process is called "parsing dates."
<br>
We can specify the
format of our dates with a guide called known as "strftime directive". The basic
idea is that you need point out which parts of the date are where and what
punctuation is used in the date. There are many possible parts of a date, but
the most common are %d for day, %m for month, %y for a two-digit year or %Y for
a four-digit year.

Some examples:
- 1/17/07 has the format "%m/%d/%y
-
17-1-2007 has the format "%d-%m-%Y"

<br> 
Looking back at the dates from our
earthquakes dataframe, we can see the format is "month/day/four-digit year". If
the date column is only in one format, parsing the dates is pretty straight
forward. However, for our dataset, we run into an issue.

```{.python .input  n=107}
# This date is not in the same format as others
earthquakes.Date[3378]
```

```{.json .output n=107}
[
 {
  "data": {
   "text/plain": "'1975-02-23T02:58:41.000Z'"
  },
  "execution_count": 107,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

In the case we have multiple date formats, you have pandas try to infer what the
right date format should be. You can accomplish this like so:

```{.python .input  n=108}
earthquakes['Date_parsed'] = pd.to_datetime(earthquakes['Date'], infer_datetime_format= True)

```

```{.python .input  n=109}
earthquakes['Date_parsed'].head()
```

```{.json .output n=109}
[
 {
  "data": {
   "text/plain": "0   1965-01-02\n1   1965-01-04\n2   1965-01-05\n3   1965-01-08\n4   1965-01-09\nName: Date_parsed, dtype: datetime64[ns]"
  },
  "execution_count": 109,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

We can now see the type of object is no longer an "Object" type, it is now
correctly labeled a datetime object.
<br>
<br>
## Select just the year from our
column
Now that we have our dates identified by Python, let's see how easy it is
to get information from this column. Say we wanted the year only:

```{.python .input  n=110}
year_earthquakes = earthquakes['Date_parsed'].dt.year
```

```{.python .input  n=111}
year_earthquakes.head()
```

```{.json .output n=111}
[
 {
  "data": {
   "text/plain": "0    1965\n1    1965\n2    1965\n3    1965\n4    1965\nName: Date_parsed, dtype: int64"
  },
  "execution_count": 111,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

 ## Plot the day of the month to check the date parsing
 One danger of parsing
dates is mixing up months and days. This is danger increases when you have
mulitple formats of dates like we had, so it doesn't hurt to double check to see
if the dates were formatted corrctly.

```{.python .input  n=112}
# Select the day of the month
day_of_month_earthquakes = earthquakes['Date_parsed'].dt.day

# check for na's(if there we must remove them)
day_of_month_earthquakes.isnull().sum()


```

```{.json .output n=112}
[
 {
  "data": {
   "text/plain": "0"
  },
  "execution_count": 112,
  "metadata": {},
  "output_type": "execute_result"
 }
]
```

Since there are no na's, we skip the removing na's step.
Let's plot:

```{.python .input  n=113}
# plot day of the month
sns.distplot(day_of_month_earthquakes, kde=False, bins = 31)
```

```{.json .output n=113}
[
 {
  "data": {
   "text/plain": "<matplotlib.axes._subplots.AxesSubplot at 0x142ebbe0>"
  },
  "execution_count": 113,
  "metadata": {},
  "output_type": "execute_result"
 },
 {
  "data": {
   "image/png": "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\n",
   "text/plain": "<matplotlib.figure.Figure at 0x14f35048>"
  },
  "metadata": {},
  "output_type": "display_data"
 }
]
```

This graph looks good and the limits of the x-axis do make sense. Looks like we
parsed the dates correctly.
<br>
That's it for Day 3. Thanks for following.
