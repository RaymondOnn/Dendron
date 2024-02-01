---
id: lkftk8i1qnvmpt3tunxgant
title: tables
desc: ''
updated: 1703050135986
created: 1698063072836
---


## Spark Database and Tables

Apache Spark is not only a set of APIs and a processing engine. It is a database (HDFS) so you can create a database as well as tables and views.

### The catalog

- The catalog is a meta-store that stores metadata information about the table and its data such as schema, table name, database name, column names, partitions, the physical location where the actual data resides.
- By default, Spark comes with an in-memory catalog which is lives and dies with the SparkSession.
- The Apache Hive meta-store was used as it is a persistent and durable meta-store.
- When you are running in a local machine, these things are created in your current directory.
- In your cluster environment, both of these are configured by your cluster admin And it would be a common location across all the Spark applications.
- Links: [Hive Tables](https://spark.apache.org/docs/latest/sql-data-sources-hive-tables.html)

### Spark Tables

- The table has got two parts.
  - Table Data: Stored as data files (in the data format of our choice) in HDFS
  - Table Metadata: Stored in catalog in HDFS

### Managed vs Unmanaged

- Spark allows you to create two types of tables
  - Managed Tables
  - Unmanaged Tables
  
#### Managed Tables

- Spark manages both the metadata and the data.
- Metadata stored in Hive meta-store in HDFS.
- Data saved inside a predefined directory location known as the `spark.sql.warehouse.dir` which is the base location where all your managed tables are stored in HDFS. Hence, no need to specify directory
- This base directory set by cluster admin is going to set this base directory location for you.
- Metadata and data will be deleted when the table is dropped
- Preferred option
  - they offer some additional features such as bucketing and sorting.
  - All the future improvements in Spark SQL will also target managed tables.

``` py
from pyspark.sql import *

from lib.logger import Log4j

if __name__ == "__main__":
    spark = SparkSession \
        .builder \
        .master("local[3]") \
        .appName("SparkSQLTableDemo") \
        .enableHiveSupport() \ # to use Hive metastore
        .getOrCreate()

    logger = Log4j(spark)

    flightTimeParquetDF = spark.read \
        .format("parquet") \
        .load("dataSource/")

    spark.sql("CREATE DATABASE IF NOT EXISTS AIRLINE_DB")
    spark.catalog.setCurrentDatabase("AIRLINE_DB")

    flightTimeParquetDF.write \
        .mode("overwrite") \
        .saveAsTable("flight_data_tbl") # save as managed table

    logger.info(spark.catalog.listTables("AIRLINE_DB"))
```

##### Saving as managed table

- Saving as managed table can be done via the `saveAsTable()`method which takes the table name and it creates a managed table in the current spark database.
- By default the managed table is saved in Spark's default database
- To save in a specific database, we can
  - Prefix the database name with table name.
  - The second option is to access the catalog and set the current database for this session.

#### Unmanaged Tables (External Tables)

- Metadata stored in Hive meta-store in HDFS. Only the metadata is deleted when the table is dropped
- Gives you the flexibility to store your data at your preferred location. In return, will require to specify the data directory location when creating table
- Designed for temporarily mapping your existing data and using it in Spark SQL. Once you are done using it, drop them,
