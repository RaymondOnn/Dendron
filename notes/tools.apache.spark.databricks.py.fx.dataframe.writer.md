---
id: 6su480o08xwxe2jprn5mqmk
title: Writer
desc: ''
updated: 1666330828012
created: 1666330828012
---
## DataFrameWriter
Interface used to write a DataFrame to external storage systems

<strong><code>
(df  
&nbsp;  .write                         
&nbsp;  .option("compression", "snappy")  
&nbsp;  .mode("overwrite")      
&nbsp;  .parquet(output_dir)       
)
</code></strong>

DataFrameWriter is accessible through the SparkSession attribute **`write`**. This class includes methods to write DataFrames to different external storage systems.

## Write DataFrames to files

Write **`users_df`** to parquet with DataFrameWriter's **`parquet`** method and the following configurations:

Snappy compression, overwrite mode

```python
%python
users_output_dir = f"{DA.paths.working_dir}/users.parquet"

(users_df
 .write
 .option("compression", "snappy")
 .mode("overwrite")
 .parquet(users_output_dir)
)

display(dbutils.fs.ls(users_output_dir))
```

As with DataFrameReader, Spark's Python API also allows you to specify the DataFrameWriter options as parameters to the **`parquet`** method

```python
%python
(users_df
 .write
 .parquet(users_output_dir, compression="snappy", mode="overwrite")
)
```

### Write DataFrames to tables

Write **`events_df`** to a table using the DataFrameWriter method **`saveAsTable`**

<img src="https://files.training.databricks.com/images/icon_note_32.png" alt="Note"> This creates a global table, unlike the local view created by the DataFrame method **`createOrReplaceTempView`**

```python
%python
events_df.write.mode("overwrite").saveAsTable("events")
```

## Delta Lake

In almost all cases, the best practice is to use Delta Lake format, especially whenever the data will be referenced from a Databricks workspace. 

<a href="https://delta.io/" target="_blank">Delta Lake</a> is an open source technology designed to work with Spark to bring reliability to data lakes.

![delta](https://files.training.databricks.com/images/aspwd/delta_storage_layer.png)

#### Delta Lake's Key Features
- ACID transactions
- Scalable metadata handling
- Unified streaming and batch processing
- Time travel (data versioning)
- Schema enforcement and evolution
- Audit history
- Parquet format
- Compatible with Apache Spark API

### Write Results to a Delta Table

Write **`events_df`** with the DataFrameWriter's **`save`** method and the following configurations: Delta format & overwrite mode.

```python
events_output_path = f"{DA.paths.working_dir}/delta/events"

(events_df
 .write
 .format("delta")
 .mode("overwrite")
 .save(events_output_path)
)
```