---
id: w7g67q7gi4ryn4lgkeefcq8
title: string
desc: ''
updated: 1666334043327
created: 1666334022450
---
### String Functions
Here are some of the built-in functions available for manipulating strings.

| Method         | Description                                                                        |
| -------------- | ---------------------------------------------------------------------------------- |
| translate      | Translate any character in the src by a character in replaceString                 |
| regexp_replace | Replace all substrings of the specified string value that match regexp with rep    |
| regexp_extract | Extract a specific group matched by a Java regex, from the specified string column |
| ltrim          | Removes the leading space characters from the specified string column              |
| lower          | Converts a string column to lowercase                                              |
| split          | Splits str around matches of the given pattern                                     |

<br>

For example: let's imagine that we need to parse our **`email`** column.   
We're going to use the **`split`** function  to split domain and handle.

```python
from pyspark.sql.functions import split

display(df.select(split(df.email, '@', 0).alias('email_handle')))
```