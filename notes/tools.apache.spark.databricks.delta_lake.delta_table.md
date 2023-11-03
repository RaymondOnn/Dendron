---
id: e9m81b44q97c27d0hybpvr0
title: Delta_table
desc: ''
updated: 1697959674403
created: 1666437695995
---

##### Documentation

- <a href="https://docs.delta.io/latest/quick-start.html#create-a-table" target="_blank">Delta Table</a>  

### Create a Delta Table

Let's first read the Parquet-format BedBricks events dataset.

```py
events_df = spark.read.format("parquet").load(f"{DA.paths.datasets}/ecommerce/events/events.parquet")
display(events_df)
```

Write the data in Delta format to the directory given by **`delta_path`**.

```py
delta_path = f"{DA.paths.working_dir}/delta-events"
events_df.write.format("delta").mode("overwrite").save(delta_path)
```

Write the data in Delta format as a managed table in the metastore.

```py
events_df.write.format("delta").mode("overwrite").saveAsTable("delta_events")
```

 As with other file formats, Delta supports partitioning your data in storage using the unique values in a specified column (often referred to as "Hive partitioning").

Let's **overwrite** the Delta dataset in the **`delta_path`** directory to partition by state. This can accelerate queries that filter by state.

```py
from pyspark.sql.functions import col

state_events_df = events_df.withColumn("state", col("geo.state"))

state_events_df.write.format("delta").mode("overwrite").partitionBy("state").option("overwriteSchema", "true").save(delta_path)
```

### Read from your Delta table

```py
df = spark.read.format("delta").load(delta_path)
display(df)
```

### Update your Delta Table

Let's filter for rows where the event takes place on a mobile device.

```py
df_update = state_events_df.filter(col("device").isin(["Android", "iOS"]))
display(df_update)


df_update.write.format("delta").mode("overwrite").save(delta_path)


df = spark.read.format("delta").load(delta_path)
display(df)
```

 Let's look at the files in the California partition post-update. Remember, the different files in this directory are snapshots of your DataFrame corresponding to different commits.

```py
display(dbutils.fs.ls(f"{delta_path}/state=CA/"))
```
