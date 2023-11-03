---
id: gmjdo4o7qaehwz0mkek4vnb
title: Transformations
desc: ''
updated: 1670232194944
created: 1666324732927
---
## Transformations
When we created **`budget_df`**, we used a series of DataFrame transformation methods e.g. **`select`**, **`where`**, **`orderBy`**.

<strong><code>products_df  
&nbsp;  .select("name", "price")  
&nbsp;  .where("price < 200")  
&nbsp;  .orderBy("price")  
</code></strong>
    
- Transformations operate on and return other DataFrames, allowing us to chain transformation methods together to construct new DataFrames.
- However, these operations can't execute on their own, as transformation methods are **lazily evaluated** i.e. running the cell does not trigger any computation.
- Basically a description of a distributed computation on Spark

## DataFrame Transformation Methods
| Method                               | Description                                                                                        |
| ------------------------------------ | -------------------------------------------------------------------------------------------------- |
| **`select`**                         | Returns a new DataFrame by computing given expression for each element                             |
| **`drop`**                           | Returns a new DataFrame with a column dropped                                                      |
| **`withColumnRenamed`**              | Returns a new DataFrame with a column renamed                                                      |
| **`withColumn`**                     | Returns a new DataFrame by adding a column or replacing the existing column that has the same name |
| **`filter`**, **`where`**            | Filters rows using the given condition                                                             |
| **`sort`**, **`orderBy`**            | Returns a new DataFrame sorted by the given expressions                                            |
| **`dropDuplicates`**, **`distinct`** | Returns a new DataFrame with duplicate rows removed                                                |
| **`limit`**                          | Returns a new DataFrame by taking the first n rows                                                 |
| **`groupBy`**                        | Groups the DataFrame using the specified columns, so we can run aggregation on them                |

<br>

### Subset columns
Use DataFrame transformations to subset columns

#### **`select()`**
Selects a list of columns or column based expressions
```python
%python
devices_df = events_df.select("user_id", "device")
display(devices_df)


# Alternatively
from pyspark.sql.functions import col
locations_df = events_df.select(
    "user_id", 
    col("geo.city").alias("city"), 
    col("geo.state").alias("state")
)
display(locations_df)
```

#### **`selectExpr()`**
Selects a list of SQL expressions
```python
%python
apple_df = events_df.selectExpr("user_id", "device in ('macOS', 'iOS') as apple_user")
display(apple_df)
```

#### **`drop()`**
Returns a new DataFrame after dropping the given column, specified as a string or Column object

Use strings to specify multiple columns
```python
%python
anonymous_df = events_df.drop("user_id", "geo", "device")
display(anonymous_df)

no_sales_df = events_df.drop(col("ecommerce"))
display(no_sales_df)
```

## Add or replace columns
Use DataFrame transformations to add or replace columns
#### **`withColumn()`**
Returns a new DataFrame by adding a column or replacing an existing column that has the same name.
```python
%python
mobile_df = events_df.withColumn("mobile", col("device").isin("iOS", "Android"))
display(mobile_df)

purchase_quantity_df = events_df.withColumn("purchase_quantity", col("ecommerce.total_item_quantity").cast("int"))
purchase_quantity_df.printSchema()
```
#### **`withColumnRenamed()`**
Returns a new DataFrame with a column renamed.

### Subset Rows
Use DataFrame transformations to subset rows

#### **`filter()`**
Filters rows using the given SQL expression or column based condition.

##### Alias: **`where`**

```python
%python
purchases_df = events_df.filter("ecommerce.total_item_quantity > 0")
display(purchases_df)

revenue_df = events_df.filter(col("ecommerce.purchase_revenue_in_usd").isNotNull())
display(revenue_df)

android_df = events_df.filter((col("traffic_source") != "direct") & (col("device") == "Android"))
display(android_df)
```

#### **`dropDuplicates()`**
Returns a new DataFrame with duplicate rows removed, optionally considering only a subset of columns.

##### Alias: **`distinct`**

```python
%python
display(events_df.distinct())

distinct_users_df = events_df.dropDuplicates(["user_id"])
display(distinct_users_df)
```

#### **`limit()`**
Returns a new DataFrame by taking the first n rows.

```python
%python
limit_df = events_df.limit(100)
display(limit_df)
```

### Sort rows
Use DataFrame transformations to sort rows

#### **`sort()`**
Returns a new DataFrame sorted by the given columns or expressions.

##### Alias: **`orderBy`**

```python
%python
increase_timestamps_df = events_df.sort("event_timestamp")
display(increase_timestamps_df)

decrease_timestamp_df = events_df.sort(col("event_timestamp").desc())
display(decrease_timestamp_df)

increase_sessions_df = events_df.orderBy(["user_first_touch_timestamp", "event_timestamp"])
display(increase_sessions_df)

decrease_sessions_df = events_df.sort(col("user_first_touch_timestamp").desc(), col("event_timestamp"))
display(decrease_sessions_df)
```


## Union and unionByName
<img src="https://files.training.databricks.com/images/icon_warn_32.png" alt="Warning"> The DataFrame <a href="https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.DataFrame.union.html" target="_blank">**`union`**</a> method resolves columns by position, as in standard SQL. You should use it only if the two DataFrames have exactly the same schema, including the column order. In contrast, the DataFrame <a href="https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.DataFrame.unionByName.html" target="_blank">**`unionByName`**</a> method resolves columns by name.  This is equivalent to UNION ALL in SQL.  Neither one will remove duplicates.  

Below is a check to see if the two dataframes have a matching schema where **`union`** would be appropriate


```python

mattress_df.schema==size_df.schema
```

If we do get the two schemas to match with a simple select statement, then we can use a union
```python
union_count = mattress_df.select("email").union(size_df.select("email")).count()

mattress_count = mattress_df.count()
size_count = size_df.count()

mattress_count + size_count == union_count
```