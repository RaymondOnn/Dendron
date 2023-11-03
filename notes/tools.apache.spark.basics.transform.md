---
id: 073rvv7knjq0yq33089r4g4
title: transform
desc: ''
updated: 1698244405575
created: 1698244278530
---


## Tranformations

### A word on Interfaces

- The DataFrame is the programmatic interface for your data
- The database table is the SQL interface of your data where we apply SQL expressions

### Types of Transformations

- Referencing Rows/Columns
- Creating and using user-defined functions
- Aggregating and summarizing i.e. grouping, windowing, and rollups
- Combining DataFrames i.e. Join and Union
- Applying functions and built-in transformationsi.e. filtering, sorting, splitting, sampling, and finding unique.
- Using and implementing built-in and column-level functions
- Creating custom expressions

### Referencing Rows/Columns

#### Rows

- Spark DataFrame is a Dataset[Row]. Each row in a DataFrame is a single record represented by an object of type Row.
- Three scenarios when you might have to work with the Row object.
  - Manually creating Rows and DataFrame

  ``` py
    from pyspark.sql import *
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    def to_date_df(df, fmt, fld):
        return df.withColumn(fld, to_date(col(fld), fmt))
    
    my_schema = StructType([
    StructField("ID", StringType()),
    StructField("EventDate", StringType())])

    my_rows = [Row("123", "04/05/2020"), Row("124", "4/5/2020"), Row("125", "04/5/2020"), Row("126", "4/05/2020")]
    my_rdd = spark.sparkContext.parallelize(my_rows, 2)  # create 2 partitions
    my_df = spark.createDataFrame(my_rdd, my_schema)
    
    my_df.printSchema()
    my_df.show()
    new_df = to_date_df(my_df,  "M/d/y", "EventDate")
    new_df.printSchema()
    new_df.show() 
  ```

  - Collecting DataFrame rows to the driver.
  
  ``` py
    from datetime import date
    from unittest import TestCase
    from pyspark.sql import *
    from pyspark.sql.types import *
    from RowDemo import to_date_df

    class RowDemoTestCase(TestCase):
        @classmethod
        def setUpClass(cls) -> None:
            cls.spark = SparkSession.builder \
                .master("local[3]") \
                .appName("RowDemoTest") \
                .getOrCreate()

            my_schema = StructType([
                StructField("ID", StringType()),
                StructField("EventDate", StringType())])

            my_rows = [Row("123", "04/05/2020"), Row("124", "4/5/2020"), Row("125", "04/5/2020"), Row("126", "4/05/2020")]
            my_rdd = cls.spark.sparkContext.parallelize(my_rows, 2)
            cls.my_df = cls.spark.createDataFrame(my_rdd, my_schema)

        def test_data_type(self):
            # collect() will return List[Row] to your driver.
            rows = to_date_df(self.my_df, "M/d/y", "EventDate").collect()
            for row in rows:
                self.assertIsInstance(row["EventDate"], date)

        def test_date_value(self):
            rows = to_date_df(self.my_df, "M/d/y", "EventDate").collect()
            for row in rows:
                self.assertEqual(row["EventDate"], date(2020, 4, 5))
    
  ```

  - Work with individual rows in Spark Transformations.

  ``` py
   logs_df \
        .where("trim(referrer) != '-' ") \
        .withColumn("referrer", substring_index("referrer", "/", 3)) \
        .groupBy("referrer") \
        .count() \
        .show(100, truncate=False)
  ```

#### Columns

- Spark DataFrame columns are objects of type Column.
- There are two ways to refer to columns in a DataFrame Transformation.
  - Column String
  
  ``` py
  
    airlinesDF.select("Origin", "Dest", "Distance" ).show(10)
  ```

  - Column Object

  ``` py
    from pyspark.sql.functions import *
    airlinesDF.select(column("Origin"), col("Dest"), "Distance").show(10)
  ```

- Spark DataFrame offers multiple ways to create column expressions.
  - String Expressions or SQL Expressions

  ``` py
    airlinesDF.selectExpr("Origin", "Dest", "Distance", "to_date(concat(Year,Month,DayofMonth),'yyyyMMdd') as FlightDate").show(10)
  ```

  - Column Object Expressions

  ``` py
    airlinesDF.select("Origin", "Dest", "Distance", to_date(concat("Year","Month","DayofMonth"),"yyyyMMdd").alias("FlightDate")).show(10)
  ```

  - NOTE: You should be referring to three places: DataFrame, Column and built-in functions

### User-Defined Functions

#### Using UDFs

- To use UDFs, there are 3 main steps
  1. Create your function.
  2. Register it as UDF and get the reference.
       - Once your function is registered in the Spark Session, your driver will serialize and send this function to the executors so that they can run this function.
  3. Use your function in your expression.

#### Implementation

- Two approaches for implementation
  - Column Object Expression
    - The withColumn() transformation allows you to transform a single column without impacting other columns in the Dataframe. It takes two arguments.The first argument is the column name that you want to transform. The next argument is a column expression.

  ``` py
  # Step 1: Define UDF
  def parse_gender(gender):
      female_pattern = r"^f$|f.m|w.m" # "|" represents OR operation
      male_pattern = r"^m$|ma|m.l"
      if re.search(female_pattern, gender.lower()):
          return "Female"
      elif re.search(male_pattern, gender.lower()):
          return "Male"
      else:
          return "Unknown"

  # Step 2: Register UDF before it can be used
  parse_gender_udf = udf(parse_gender, returnType=StringType())
  
  # Check catalog for UDF
  logger.info("Catalog Entry:")
  [logger.info(r) for r in spark.catalog.listFunctions() if "parse_gender" in r.name]

  # Step  3: Using UDF
  survey_df2 = survey_df.withColumn("Gender", parse_gender_udf("Gender"))
  survey_df2.show(10)

  ```

  - String Expression

  ``` py
  # Step 1: Define UDF
  def parse_gender(gender):
      female_pattern = r"^f$|f.m|w.m" # "|" represents OR operation
      male_pattern = r"^m$|ma|m.l"
      if re.search(female_pattern, gender.lower()):
          return "Female"
      elif re.search(male_pattern, gender.lower()):
          return "Male"
      else:
          return "Unknown"

  # Step 2: Register UDF as SQL function
  # Note: An entry is made in the catalog upon registration
  spark.udf.register("parse_gender_udf", parse_gender, StringType())
  
  # Check catalog for UDF
  logger.info("Catalog Entry:")
  [logger.info(r) for r in spark.catalog.listFunctions() if "parse_gender" in r.name]

  # Step  3: Using UDF
  survey_df3 = survey_df.withColumn("Gender", expr("parse_gender_udf(Gender)"))
  survey_df3.show(10)

  ```

### Aggregations

- Aggregations can be classified into three broad categories.
  - Simple Aggregations for e.g. avg(), count(), max(), min(),, sum()
  - Grouping Aggregations
  - Windowing Aggregations and summarization.

#### Simple Aggregations

- The simple aggregation will give you a one-line summary.
- For e.g. counting the number of records in a DataFrame and returning a single row with the count of records.
- Can be used in column object expression and in SQL like string expressions.

  ``` py
  # column object expression form
  invoice_df.select(f.count("*").alias("Count *"),
                      f.sum("Quantity").alias("TotalQuantity"),
                      f.avg("UnitPrice").alias("AvgPrice"),
                      f.countDistinct("InvoiceNo").alias("CountDistinct")
                      ).show()
    
    # SQL Expression Form
    invoice_df.selectExpr(
        "count(1) as `count 1`",
        "count(StockCode) as `count field`",
        "sum(Quantity) as TotalQuantity",
        "avg(UnitPrice) as AvgPrice"
    ).show()
  
  ```

#### Grouping Aggregations

- Spark SQL is an excellent and easy method to run your grouping aggregations.

  ``` py
  invoice_df.createOrReplaceTempView("sales")
  summary_sql = spark.sql("""
        SELECT Country, InvoiceNo,
              sum(Quantity) as TotalQuantity,
              round(sum(Quantity*UnitPrice),2) as InvoiceValue
        FROM sales
        GROUP BY Country, InvoiceNo""")
  ```

- Using Dataframe expressions, it is a two step process
  1. Select the columns to group by using the `df.groupBy(<COL_NAME>, <COL_NAME>, ...)` method or the `df.agg()` method
  2. Provide the list of aggregations into the `.agg()` method

``` py
NumInvoices = f.countDistinct("InvoiceNo").alias("NumInvoices")
TotalQuantity = f.sum("Quantity").alias("TotalQuantity")
InvoiceValue = f.expr("round(sum(Quantity * UnitPrice),2) as InvoiceValue")

exSummary_df = invoice_df \
    .withColumn("InvoiceDate", f.to_date(f.col("InvoiceDate"), "dd-MM-yyyy H.mm")) \
    .where("year(InvoiceDate) == 2010") \
    .withColumn("WeekNumber", f.weekofyear(f.col("InvoiceDate"))) \
    .groupBy("Country", "WeekNumber") \
    .agg(NumInvoices, TotalQuantity, InvoiceValue)
```

#### Windowing Aggregates

- using them is a three-step process.(SQL: AGG() )
  - Identify your partitioning columns (SQL: PARTITION BY)
  - Identify your ordering requirement (SQL: ORDER BY)
  - define your window start and end.
- Defining the Window Object will require three things
  - Partition
  - Ordering
  - the window start/end

``` py
summary_df = spark.read.parquet("data/summary.parquet")
# Define window
running_total_window = Window \
                          .partitionBy("Country") \
                          .orderBy("WeekNumber") \
                          .rowsBetween(-2, Window.currentRow)
# Window Aggregation
summary_df.withColumn(
              "RunningTotal",
              f.sum("InvoiceValue").over(running_total_window)
          ) \
          .show()
```

### Joins

- Joins are all about bringing together two data frames
- And we combine these left and right data frames using two things.
  - The join condition or the join expression.
  - the join type
- Start with the left data frame and pass-in the right data frame as the first argument to the join method. Make sense?

The join method takes two more arguments.
Join Expression and Join Type.
The inner join is the default join type. So you can skip the third argument if you want to perform an inner join.

#### The Column Name Ambiguity Problem

- Every Dataframe column has a unique ID in the catalog, and the Spark engine always works using those internal ids
- Since we are expected to work with the column names. the spark engine will translate these column names to ids during the analysis phase.
- If multiple columns have the same column name, the spark gets confused and throws this error.
- This is not an issue when using select *, translation is not needed since Spark takes all the column ids and shows them.

##### Solutions

- We have two approaches to avoid such ambiguity.
- The first approach is to rename ambiguous columns, even before joining these two data frames.
- The other approach is to drop the ambiguous column after the join.

``` py
join_expr = order_df.prod_id == product_df.prod_id

product_renamed_df = product_df.withColumnRenamed("qty", "reorder_qty")

order_df.join(product_renamed_df, join_expr, "inner") \
    .drop(product_renamed_df.prod_id) \
    .select("order_id", "prod_id", "prod_name", "unit_price", "list_price", "qty") \
    .show()
```
