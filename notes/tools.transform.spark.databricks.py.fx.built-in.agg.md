---
id: l21i4ck046e77mysti8ew7p
title: Agg
desc: ''
updated: 1666472757638
created: 1666427015194
---

### Aggregate Functions

Here are some of the built-in functions available for aggregation.

| Method                | Description                                                        |
| --------------------- | ------------------------------------------------------------------ |
| approx_count_distinct | Returns the approximate number of distinct items in a group        |
| avg                   | Returns the average of the values in a group                       |
| collect_list          | Returns a list of objects with duplicates                          |
| corr                  | Returns the Pearson Correlation Coefficient for two columns        |
| max                   | Compute the max value for each numeric columns for each group      |
| mean                  | Compute the average value for each numeric columns for each group  |
| stddev_samp           | Returns the sample standard deviation of the expression in a group |
| sumDistinct           | Returns the sum of distinct values in the expression               |
| var_pop               | Returns the population variance of the values in a group           |

Use the grouped data method <a href="https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.GroupedData.agg.html#pyspark.sql.GroupedData.agg" target="_blank">**`agg`**</a> to apply built-in aggregate functions

This allows you to apply other transformations on the resulting columns, such as <a href="https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.Column.alias.html" target="_blank">**`alias`**</a>.

```python
%python

from pyspark.sql.functions import sum

state_purchases_df = df.groupBy("geo.state").agg(sum("ecommerce.total_item_quantity").alias("total_purchases"))
display(state_purchases_df)


# Apply multiple aggregate functions on grouped data
from pyspark.sql.functions import avg, approx_count_distinct

state_aggregates_df = (df
                       .groupBy("geo.state")
                       .agg(avg("ecommerce.total_item_quantity").alias("avg_quantity"),
                            approx_count_distinct("user_id").alias("distinct_users"))
                      )

display(state_aggregates_df)
```

