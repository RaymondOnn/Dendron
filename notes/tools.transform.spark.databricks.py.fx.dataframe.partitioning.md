---
id: cp7v6ad51nbv0k3x42179kf
title: Partitioning
desc: ''
updated: 1666436975251
created: 1666346271856
---

### Get partitions and cores

Use the **`rdd`** method **`getNumPartitions`** to get the number of DataFrame partitions.

```python
df = spark.read.format("delta").load(DA.paths.events)
df.rdd.getNumPartitions()
```

Access **`SparkContext`** through **`SparkSession`** to get the number of cores or slots.

Use the **`defaultParallelism`** attribute to get the number of cores in a cluster.

```python
print(spark.sparkContext.defaultParallelism)
```

**`SparkContext`** is also provided in Databricks notebooks as the variable **`sc`**.

```py
print(sc.defaultParallelism)
```

### Repartition DataFrame

There are two methods available to repartition a DataFrame: **`repartition`** and **`coalesce`**.

#### **`repartition`**
Returns a new DataFrame that has exactly **`n`** partitions.

- Wide transformation
- Pro: Evenly balances partition sizes  
- Con: Requires shuffling all data

```py
repartitioned_df = df.repartition(8)

repartitioned_df.rdd.getNumPartitions()
```        
#### **`coalesce`**
Returns a new DataFrame that has exactly **`n`** partitions, when fewer partitions are requested.

If a larger number of partitions is requested, it will stay at the current number of partitions.

- Narrow transformation, some partitions are effectively concatenated
- Pro: Requires no shuffling
- Cons:
  - Is not able to increase # partitions
  - Can result in uneven partition sizes

```py
coalesce_df = df.coalesce(8)

coalesce_df.rdd.getNumPartitions()
```

### Configure default shuffle partitions

Use the SparkSession's **`conf`** attribute to get and set dynamic Spark configuration properties. The **`spark.sql.shuffle.partitions`** property determines the number of partitions that result from a shuffle. Let's check its default value:

```py
spark.conf.get("spark.sql.shuffle.partitions")

# Assuming that the data set isn't too large, you could configure the default number of shuffle partitions to match the number of cores:
spark.conf.set("spark.sql.shuffle.partitions", spark.sparkContext.defaultParallelism)
print(spark.conf.get("spark.sql.shuffle.partitions"))
```

### Partitioning Guidelines
- Make the number of partitions a multiple of the number of cores
- Target a partition size of ~200MB
- Size default shuffle partitions by dividing largest shuffle stage input by the target partition size (e.g., 4TB / 200MB = 20,000 shuffle partition count)

<img src="https://files.training.databricks.com/images/icon_note_32.png" alt="Note"> When writing a DataFrame to storage, the number of DataFrame partitions determines the number of data files written. (This assumes that <a href="https://sparkbyexamples.com/apache-hive/hive-partitions-explained-with-examples/" target="_blank">Hive partitioning</a> is not used for the data in storage. A discussion of DataFrame partitioning vs Hive partitioning is beyond the scope of this class.)

### Adaptive Query Execution

<img src="https://files.training.databricks.com/images/aspwd/partitioning_aqe.png" width="60%" />

In Spark 3, <a href="https://spark.apache.org/docs/latest/sql-performance-tuning.html#adaptive-query-execution" target="_blank">AQE</a> is now able to <a href="https://databricks.com/blog/2020/05/29/adaptive-query-execution-speeding-up-spark-sql-at-runtime.html" target="_blank"> dynamically coalesce shuffle partitions</a> at runtime. This means that you can set **`spark.sql.shuffle.partitions`** based on the largest data set your application processes and allow AQE to reduce the number of partitions automatically when there is less data to process.

The **`spark.sql.adaptive.enabled`** configuration option controls whether AQE is turned on/off.

```py
spark.conf.get("spark.sql.adaptive.enabled")
```