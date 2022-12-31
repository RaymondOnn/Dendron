---
id: cbdukdlszs8ltxzw76dex5a
title: Misc
desc: ''
updated: 1666428409095
created: 1666426918489
---

### Non-aggregate and Miscellaneous Functions
Here are a few additional non-aggregate and miscellaneous built-in functions.

| Method       | Description                                                                                                                |
| ------------ | -------------------------------------------------------------------------------------------------------------------------- |
| col / column | Returns a Column based on the given column name.                                                                           |
| lit          | Creates a Column of literal value                                                                                          |
| isnull       | Return true iff the column is null                                                                                         |
| rand         | Generate a random column with independent and identically distributed (i.i.d.) samples uniformly distributed in [0.0, 1.0) |
<br>

We could select a particular column using the **`col`** function
```python
gmail_accounts = sales_df.filter(col("email").endswith("gmail.com"))

display(gmail_accounts)
```
**`lit`** can be used to create a column out of a value, which is useful for appending columns.  

```python
display(gmail_accounts.select("email", lit(True).alias("gmail user")))
```

