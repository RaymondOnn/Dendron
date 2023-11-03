---
id: 14hruag9267ztot1one2koz
title: Actions
desc: ''
updated: 1670232023151
created: 1666324863719
---
## Actions
- Conversely, DataFrame actions are methods that **trigger computation** and outputs non-DataFrame values.
- Actions are needed to trigger the execution of any DataFrame transformations.

The **`show`** action causes the following cell to execute transformations.

```python
(products_df
  .select("name", "price")
  .where("price < 200")
  .orderBy("price")
  .show())
```

Below are several examples of <a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql.html#dataframe-apis" target="_blank">DataFrame</a> actions.

### DataFrame Action Methods
| Method             | Description                                                                   |
| ------------------ | ----------------------------------------------------------------------------- |
| show               | Displays the top n rows of DataFrame in a tabular form                        |
| count              | Returns the number of rows in the DataFrame i.e. `df.count()`                 |
| describe,  summary | Computes basic statistics for numeric and string columns                      |
| first, head        | Returns the the first row                                                     |
| collect            | Returns an array that contains all rows in this DataFrame i.e. `df.collect()` |
| take               | Returns an array of the first n rows in the DataFrame                         |