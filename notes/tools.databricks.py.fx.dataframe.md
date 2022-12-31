---
id: ff5nzbcnf41275xl9u9i7co
title: Dataframe
desc: ''
updated: 1666328155663
created: 1666328109729
---

#### **`SparkSession`** Methods
Below are several additional methods we can use to create DataFrames. All of these can be found in the <a href="https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/spark_session.html" target="_blank">documentation</a> for **`SparkSession`**.

| Method          | Description                                                                                                                                                       |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sql             | Returns a DataFrame representing the result of the given query i.e.  `result_df = spark.sql("SELECT name, price FROM products WHERE price < 200 ORDER BY price")` |
| table           | Returns the specified table as a DataFrame i.e. `products_df = spark.table("products")`                                                                           |
| read            | Returns a DataFrameReader that can be used to read data in as a DataFrame                                                                                         |
| range           | Create a DataFrame with a column containing elements in a range from start to end (exclusive) with step value and number of partitions                            |
| createDataFrame | Creates a DataFrame from a list of tuples, primarily used for testing i.e. `df.schema`                                                                            |
| schema          | Access a dataframe's schema. The schema defines the column names and types of a dataframe                                                                         |
| printSchema     | View nicer output of schema i.e. `df.printSchema()`                                                                                                               |
