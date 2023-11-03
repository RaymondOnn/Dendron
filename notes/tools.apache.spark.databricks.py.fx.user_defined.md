---
id: g7a9k79lpxifm516yhrmuoe
title: User_defined
desc: ''
updated: 1666345734821
created: 1666335195716
---
### User-Defined Function (UDF)
A custom column transformation function

- Can’t be optimized by Catalyst Optimizer
- Function is serialized and sent to executors
- Row data is deserialized from Spark's native binary format to pass to the UDF, and the results are serialized back into Spark's native format
- For Python UDFs, additional interprocess communication overhead between the executor and a Python interpreter running on each worker node

### Define a function

Define a function (on the driver) to get the first letter of a string from the **`email`** field.

```python
%python

def first_letter_function(email):
    return email[0]

first_letter_function("annagray@kaufman.com")
```
### Create and apply UDF
Register the function as a UDF. This serializes the function and sends it to executors to be able to transform DataFrame records.

```python
first_letter_udf = udf(first_letter_function)

# Apply the UDF on the **`email`** column.
from pyspark.sql.functions import col

display(sales_df.select(first_letter_udf(col("email"))))
```

### Register UDF to use in SQL
Register the UDF using **`spark.udf.register`** to also make it available for use in the SQL namespace.

```python
sales_df.createOrReplaceTempView("sales")

first_letter_udf = spark.udf.register("sql_udf", first_letter_function)

```

### Use Decorator Syntax (Python Only)

Alternatively, you can define and register a UDF using <a href="https://realpython.com/primer-on-python-decorators/" target="_blank">Python decorator syntax</a>. The **`@udf`** decorator parameter is the Column datatype the function returns.

You will no longer be able to call the local Python function (i.e., **`first_letter_udf("annagray@kaufman.com")`** will not work).

<img src="https://files.training.databricks.com/images/icon_note_32.png" alt="Note"> This example also uses <a href="https://docs.python.org/3/library/typing.html" target="_blank">Python type hints</a>, which were introduced in Python 3.5. Type hints are not required for this example, but instead serve as "documentation" to help developers use the function correctly. They are used in this example to emphasize that the UDF processes one record at a time, taking a single **`str`** argument and returning a **`str`** value.

```python
%python
# Our input/output is a string
@udf("string")
def first_letter_udf(email: str) -> str:
    return email[0]
```
And let's use our decorator UDF here.

```python
from pyspark.sql.functions import col

sales_df = spark.read.format("delta").load(DA.paths.sales)
display(sales_df.select(first_letter_udf(col("email"))))
```

### Pandas/Vectorized UDFs

Pandas UDFs are available in Python to improve the efficiency of UDFs. Pandas UDFs utilize Apache Arrow to speed up computation.

* <a href="https://databricks.com/blog/2017/10/30/introducing-vectorized-udfs-for-pyspark.html" target="_blank">Blog post</a>
* <a href="https://spark.apache.org/docs/latest/api/python/user_guide/sql/arrow_pandas.html?highlight=arrow" target="_blank">Documentation</a>

<img src="https://databricks.com/wp-content/uploads/2017/10/image1-4.png" alt="Benchmark" width ="600" height="350">

The user-defined functions are executed using: 
* <a href="https://arrow.apache.org/" target="_blank">Apache Arrow</a>, an in-memory columnar data format that is used in Spark to efficiently transfer data between JVM and Python processes with near-zero (de)serialization cost
* Pandas inside the function, to work with Pandas instances and APIs

<img src="https://files.training.databricks.com/images/icon_warn_32.png" alt="Warning"> As of Spark 3.0, you should **always** define your Pandas UDF using Python type hints.

```python
import pandas as pd
from pyspark.sql.functions import pandas_udf

# We have a string input/output
@pandas_udf("string")
def vectorized_udf(email: pd.Series) -> pd.Series:
    return email.str[0]

# Alternatively
# def vectorized_udf(email: pd.Series) -> pd.Series:
#     return email.str[0]
# vectorized_udf = pandas_udf(vectorized_udf, "string")

display(sales_df.select(vectorized_udf(col("email"))))
```

We can also register these Pandas UDFs to the SQL namespace.

```python
spark.udf.register("sql_vectorized_udf", vectorized_udf)
```

```sql
%sql
-- Use the Pandas UDF from SQL
SELECT sql_vectorized_udf(email) AS firstLetter FROM sales
```