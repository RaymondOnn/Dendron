---
id: 87nroi1j400umungpr7n4d0
title: Reader
desc: ''
updated: 1666328769968
created: 1666328769968
---
## DataFrameReader
Interface used to load a DataFrame from external storage systems

**`spark.read.parquet("path/to/files")`**

DataFrameReader is accessible through the SparkSession attribute **`read`**. This class includes methods to load DataFrames from different external storage systems.

### Read from CSV files
Read from CSV with the DataFrameReader's **`csv`** method and the following options:

- Tab separator, 
- use first line as header, 
- infer schema

```python
%python
users_csv_path = f"{DA.paths.datasets}/ecommerce/users/users-500k.csv"

# load data from csv to dataframe
users_df = (spark
           .read
           .option("sep", "\t")
           .option("header", True)
           .option("inferSchema", True)
           .csv(users_csv_path)
          )

users_df.printSchema()

# ----------------------------------------------------------------

# Spark's Python API also allows you to specify the DataFrameReader options as parameters to the csv method
users_df = (spark
           .read
           .csv(users_csv_path, sep="\t", header=True, inferSchema=True)
          )

users_df.printSchema()
```
<br>

Manually define the schema by creating a StructType with column names and data types

```python
%python
from pyspark.sql.types import LongType, StringType, StructType, StructField

user_defined_schema = StructType([
    StructField("user_id", StringType(), True),
    StructField("user_first_touch_timestamp", LongType(), True),
    StructField("email", StringType(), True)
])

# Read from CSV using this user-defined schema instead of inferring the schema
users_df = (spark
           .read
           .option("sep", "\t")
           .option("header", True)
           .schema(user_defined_schema)
           .csv(users_csv_path)
          )
```

Alternatively, define the schema using data definition language (DDL) syntax.
```python
ddl_schema = "user_id string, user_first_touch_timestamp long, email string"

users_df = (spark
           .read
           .option("sep", "\t")
           .option("header", True)
           .schema(ddl_schema)
           .csv(users_csv_path)
          )
```

### Read from JSON files

Read from JSON with DataFrameReader's **`json`** method and the infer schema option

```python
events_json_path = f"{DA.paths.datasets}/ecommerce/events/events-500k.json"

events_df = (spark
            .read
            .option("inferSchema", True)
            .json(events_json_path)
           )

events_df.printSchema()
```

Read data faster by creating a StructType with the schema names and data types
```python
from pyspark.sql.types import ArrayType, DoubleType, IntegerType, LongType, StringType, StructType, StructField

user_defined_schema = StructType([
    StructField("device", StringType(), True),
    StructField("ecommerce", StructType([
        StructField("purchaseRevenue", DoubleType(), True),
        StructField("total_item_quantity", LongType(), True),
        StructField("unique_items", LongType(), True)
    ]), True),
    StructField("event_name", StringType(), True),
    StructField("event_previous_timestamp", LongType(), True),
    StructField("event_timestamp", LongType(), True),
    StructField("geo", StructType([
        StructField("city", StringType(), True),
        StructField("state", StringType(), True)
    ]), True),
    StructField("items", ArrayType(
        StructType([
            StructField("coupon", StringType(), True),
            StructField("item_id", StringType(), True),
            StructField("item_name", StringType(), True),
            StructField("item_revenue_in_usd", DoubleType(), True),
            StructField("price_in_usd", DoubleType(), True),
            StructField("quantity", LongType(), True)
        ])
    ), True),
    StructField("traffic_source", StringType(), True),
    StructField("user_first_touch_timestamp", LongType(), True),
    StructField("user_id", StringType(), True)
])

events_df = (spark
            .read
            .schema(user_defined_schema)
            .json(events_json_path)
           )
```

You can use the **`StructType`** Scala method **`toDDL`** to have a DDL-formatted string created for you.

This is convenient when you need to get the DDL-formated string for ingesting CSV and JSON but you don't want to hand craft it or the **`StructType`** variant of the schema.

However, this functionality is not available in Python but the power of the notebooks allows us to use both languages.

```python

# Step 1 - use this trick to transfer a value (the dataset path) between Python and Scala using the shared spark-config
spark.conf.set("whatever_your_scope.events", events_json_path)

// Step 2 - pull the value from the config (or copy & paste it)
%scala
val eventsJsonPath = spark.conf.get("whatever_your_scope.events")

// Step 3 - Read in the JSON, but let it infer the schema
val eventsSchema = spark.read
                        .option("inferSchema", true)
                        .json(eventsJsonPath)
                        .schema.toDDL

// Step 4 - print the schema, select it, and copy it.
println("="*80)
println(eventsSchema)
println("="*80)

# Step 5 - paste the schema from above and assign it to a variable as seen here
events_schema = "`device` STRING,`ecommerce` STRUCT<`purchase_revenue_in_usd`: DOUBLE, `total_item_quantity`: BIGINT, `unique_items`: BIGINT>,`event_name` STRING,`event_previous_timestamp` BIGINT,`event_timestamp` BIGINT,`geo` STRUCT<`city`: STRING, `state`: STRING>,`items` ARRAY<STRUCT<`coupon`: STRING, `item_id`: STRING, `item_name`: STRING, `item_revenue_in_usd`: DOUBLE, `price_in_usd`: DOUBLE, `quantity`: BIGINT>>,`traffic_source` STRING,`user_first_touch_timestamp` BIGINT,`user_id` STRING"

# Step 6 - Read in the JSON data using our new DDL formatted string
events_df = (spark.read
                 .schema(events_schema)
                 .json(events_json_path))

display(events_df)
```

This is a great "trick" for producing a schema for a net-new dataset and for accelerating development.

When you are done (e.g. for Step #7), make sure to delete your temporary code.

<img src="https://files.training.databricks.com/images/icon_warn_32.png"> WARNING: **Do not use this trick in production**</br>
the inference of a schema can be REALLY slow as it<br/>
forces a full read of the source dataset to infer the schema