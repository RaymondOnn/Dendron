---
id: 37qvbad3pji30bvyvqccf9w
title: SQL
desc: ''
updated: 1670234392120
created: 1666323453425
---
## Multiple Interfaces
- Spark SQL is a module for structured data processing with multiple interfaces.
- Basically a RDD generator. RDD API is used to manipulate data based on partitions. 
- Partitions are scheduled for execution as tasks for Spark executors 

We can interact with Spark SQL in two ways:
1. Executing SQL queries
1. Working with the DataFrame API.

**Method 1: Executing SQL queries**

This is how we interacted with Spark SQL in the previous lesson.
```sql
%sql
SELECT name, price
FROM products
WHERE price < 200
ORDER BY price
```

**Method 2: Working with the DataFrame API**

We can also express Spark SQL queries using the DataFrame API.
The following cell returns a DataFrame containing the same results as those retrieved above.
```python
%python
display(spark
        .table("products")
        .select("name", "price")
        .where("price < 200")
        .orderBy("price")
       )
```       

## Query Execution
We can express the same query using any interface. The Spark SQL engine generates the same query plan used to optimize and execute on our Spark cluster.

![query execution engine](https://files.training.databricks.com/images/aspwd/spark_sql_query_execution_engine.png)

<img src="https://files.training.databricks.com/images/icon_note_32.png" alt="Note"> Resilient Distributed Datasets (RDDs) are the low-level representation of datasets processed by a Spark cluster. In early versions of Spark, you had to write <a href="https://spark.apache.org/docs/latest/rdd-programming-guide.html" target="_blank">code manipulating RDDs directly</a>. In modern versions of Spark you should instead use the higher-level DataFrame APIs, which Spark automatically compiles into low-level RDD operations.

## Spark API Documentation

To learn how we work with DataFrames in Spark SQL, let's first look at the Spark API documentation.
The main Spark <a href="https://spark.apache.org/docs/latest/" target="_blank">documentation</a> page includes links to API docs and helpful guides for each version of Spark.

The <a href="https://spark.apache.org/docs/latest/api/scala/org/apache/spark/index.html" target="_blank">Scala API</a> and <a href="https://spark.apache.org/docs/latest/api/python/index.html" target="_blank">Python API</a> are most commonly used, and it's often helpful to reference the documentation for both languages.
Scala docs tend to be more comprehensive, and Python docs tend to have more code examples.

#### Navigating Docs for the Spark SQL Module
Find the Spark SQL module by navigating to **`org.apache.spark.sql`** in the Scala API or **`pyspark.sql`** in the Python API.
The first class we'll explore in this module is the **`SparkSession`** class. You can find this by entering "SparkSession" in the search bar.

## SparkSession
The **`SparkSession`** class is the single entry point to all functionality in Spark using the DataFrame API.

In Databricks notebooks, the SparkSession is created for you, stored in a variable called **`spark`**.

```sql
%sql
spark
```

## Convert between DataFrames and SQL
**`createOrReplaceTempView`** creates a temporary view based on the DataFrame. The lifetime of the temporary view is tied to the SparkSession that was used to create the DataFrame.

```python
%python
# storing a snapshot of the table into a view using dataframe
budget_df.createOrReplaceTempView("budget")
# Display records from view
display(spark.sql("SELECT * FROM budget"))
```