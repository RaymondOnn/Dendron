---
id: bpik229ckf1pqedxy983u38
title: Groupby
desc: ''
updated: 1666332783898
created: 1666332669006
---
### Grouping data

<img src="https://files.training.databricks.com/images/aspwd/aggregation_groupby.png" width="60%" />

### groupBy
Use the DataFrame **`groupBy`** method to create a grouped data object. 

This grouped data object is called **`RelationalGroupedDataset`** in Scala and **`GroupedData`** in Python.

```python
%python
df.groupBy("event_name")

df.groupBy("geo.state", "geo.city")
```

 ### Grouped data methods
Various aggregation methods are available on the <a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/grouping.html" target="_blank">GroupedData</a> object.


| Method | Description                                                                     |
| ------ | ------------------------------------------------------------------------------- |
| agg    | Compute aggregates by specifying a series of aggregate columns                  |
| avg    | Compute the mean value for each numeric columns for each group                  |
| count  | Count the number of rows for each group                                         |
| max    | Compute the max value for each numeric columns for each group                   |
| mean   | Compute the average value for each numeric columns for each group               |
| min    | Compute the min value for each numeric column for each group                    |
| pivot  | Pivots a column of the current DataFrame and performs the specified aggregation |
| sum    | Compute the sum for each numeric columns for each group                         |

```python
%python

event_counts_df = df.groupBy("event_name").count()
display(event_counts_df)

avg_state_purchases_df = df.groupBy("geo.state").avg("ecommerce.purchase_revenue_in_usd")
display(avg_state_purchases_df)

# the total quantity and sum of the purchase revenue for each combination of state and city.
city_purchase_quantities_df = df.groupBy("geo.state", "geo.city").sum("ecommerce.total_item_quantity", "ecommerce.purchase_revenue_in_usd")
display(city_purchase_quantities_df)
```

