---
id: jdjmssu6fqsfi9zhizjf6mz
title: Nulls
desc: ''
updated: 1666334960817
created: 1666334943309
---
### DataFrameNaFunctions
<a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrameNaFunctions.html#pyspark.sql.DataFrameNaFunctions" target="_blank">DataFrameNaFunctions</a> is a DataFrame submodule with methods for handling null values. Obtain an instance of DataFrameNaFunctions by accessing the **`na`** attribute of a DataFrame.

| Method  | Description                                                                                                                          |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| drop    | Returns a new DataFrame omitting rows with any, all, or a specified number of null values, considering an optional subset of columns |
| fill    | Replace null values with the specified value for an optional subset of columns                                                       |
| replace | Returns a new DataFrame replacing a value with another value, considering an optional subset of columns                              |

<br>

```python
# Here we'll see the row count before and after dropping rows with null/NA values.  
print(sales_df.count())
print(sales_df.na.drop().count())

# Since the row counts are the same, we have the no null columns. We'll need to explode items to find some nulls in columns such as items.coupon.
sales_exploded_df = sales_df.withColumn("items", explode(col("items")))
display(sales_exploded_df.select("items.coupon"))
print(sales_exploded_df.select("items.coupon").count())
print(sales_exploded_df.select("items.coupon").na.drop().count())

# We can fill in the missing coupon codes with na.fill
display(sales_exploded_df.select("items.coupon").na.fill("NO COUPON"))