---
id: ixqo5zrfcpbzp5k7kvmhenr
title: Column
desc: ''
updated: 1666331369736
created: 1666331254802
---
## Column Expressions

A <a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/column.html" target="_blank">Column</a> is a logical construction that will be computed based on the data in a DataFrame using an expression

Construct a new Column based on existing columns in a DataFrame

```python
%python
from pyspark.sql.functions import col

print(events_df.device)
print(events_df["device"])
print(col("device"))
```

Scala supports an additional syntax for creating a new Column based on existing columns in a DataFrame
```scala
%scala
$"device"
```

### Column Operators and Methods
| Method                   | Description                                                                 |
| ------------------------ | --------------------------------------------------------------------------- |
| \*, + , <, >=            | Math and comparison operators                                               |
| ==, !=                   | Equality and inequality tests (Scala operators are **`===`** and **`=!=`**) |
| alias                    | Gives the column an alias                                                   |
| cast, astype             | Casts the column to a different data type                                   |
| isNull, isNotNull, isNan | Is null, is not null, is NaN                                                |
| asc, desc                | Returns a sort expression based on ascending/descending order of the column |
 
<br>
Create complex expressions with existing columns, operators, and methods.

```python
col("ecommerce.purchase_revenue_in_usd") + col("ecommerce.total_item_quantity")
col("event_timestamp").desc()
(col("ecommerce.purchase_revenue_in_usd") * 100).cast("int")
```

Here's an example of using these column expressions in the context of a DataFrame
```python
%python
rev_df = (events_df
         .filter(col("ecommerce.purchase_revenue_in_usd").isNotNull())
         .withColumn("purchase_revenue", (col("ecommerce.purchase_revenue_in_usd") * 100).cast("int"))
         .withColumn("avg_purchase_revenue", col("ecommerce.purchase_revenue_in_usd") / col("ecommerce.total_item_quantity"))
         .sort(col("avg_purchase_revenue").desc())
        )

display(rev_df)
```
## DataFrame Transformation Methods
| Method | Description |
| --- | --- |
| **`select`** | Returns a new DataFrame by computing given expression for each element |
| **`drop`** | Returns a new DataFrame with a column dropped |
| **`withColumnRenamed`** | Returns a new DataFrame with a column renamed |
| **`withColumn`** | Returns a new DataFrame by adding a column or replacing the existing column that has the same name |
| **`filter`**, **`where`** | Filters rows using the given condition |
| **`sort`**, **`orderBy`** | Returns a new DataFrame sorted by the given expressions |
| **`dropDuplicates`**, **`distinct`** | Returns a new DataFrame with duplicate rows removed |
| **`limit`** | Returns a new DataFrame by taking the first n rows |
| **`groupBy`** | Groups the DataFrame using the specified columns, so we can run aggregation on them |
