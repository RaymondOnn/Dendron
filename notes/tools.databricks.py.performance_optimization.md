---
id: d7ekw2d0kylgt1y6ddxbarn
title: Performance_optimization
desc: ''
updated: 1666346200981
created: 1666345823362
---

### Logical Optimization

**`explain(..)`** prints the query plans, optionally formatted by a given explain mode. Compare the following logical plan & physical plan, noting how Catalyst handled the multiple **`filter`** transformations.

```python
from pyspark.sql.functions import col

limit_events_df = (df
                   .filter(col("event_name") != "reviews")
                   .filter(col("event_name") != "checkout")
                   .filter(col("event_name") != "register")
                   .filter(col("event_name") != "email_coupon")
                   .filter(col("event_name") != "cc_info")
                   .filter(col("event_name") != "delivery")
                   .filter(col("event_name") != "shipping_info")
                   .filter(col("event_name") != "press")
                  )

limit_events_df.explain(True)
```
Of course, we could have written the query originally using a single **`filter`** condition ourselves. Compare the previous and following query plans.

```python
better_df = (df
             .filter((col("event_name").isNotNull()) &
                     (col("event_name") != "reviews") &
                     (col("event_name") != "checkout") &
                     (col("event_name") != "register") &
                     (col("event_name") != "email_coupon") &
                     (col("event_name") != "cc_info") &
                     (col("event_name") != "delivery") &
                     (col("event_name") != "shipping_info") &
                     (col("event_name") != "press"))
            )

better_df.explain(True)
```

Of course, we wouldn't write the following code intentionally, but in a long, complex query you might not notice the duplicate filter conditions. Let's see what Catalyst does with this query.

```python
stupid_df = (df
             .filter(col("event_name") != "finalize")
             .filter(col("event_name") != "finalize")
             .filter(col("event_name") != "finalize")
             .filter(col("event_name") != "finalize")
             .filter(col("event_name") != "finalize")
            )

stupid_df.explain(True)
```

### Caching

By default the data of a DataFrame is present on a Spark cluster only while it is being processed during a query -- it is not automatically persisted on the cluster afterwards. (Spark is a data processing engine, not a data storage system.) You can explicity request Spark to persist a DataFrame on the cluster by invoking its **`cache`** method.

If you do cache a DataFrame, you should always explictly evict it from cache by invoking **`unpersist`** when you no longer need it.

<img src="https://files.training.databricks.com/images/icon_best_32.png" alt="Best Practice"> Caching a DataFrame can be appropriate if you are certain that you will use the same DataFrame multiple times, as in:

- Exploratory data analysis
- Machine learning model training

<img src="https://files.training.databricks.com/images/icon_warn_32.png" alt="Warning"> Aside from those use cases, you should **not** cache DataFrames because it is likely that you'll *degrade* the performance of your application.

- Caching consumes cluster resources that could otherwise be used for task execution
- Caching can prevent Spark from performing query optimizations, as shown in the next example

### Predicate Pushdown

Here is example reading from a JDBC source, where Catalyst determines that *predicate pushdown* can take place.

```scala
%scala
// Ensure that the driver class is loaded
Class.forName("org.postgresql.Driver")
```

```python
jdbc_url = "jdbc:postgresql://54.213.33.240/training"

# Username and Password w/read-only rights
conn_properties = {
    "user" : "training",
    "password" : "training"
}

pp_df = (spark
         .read
         .jdbc(url=jdbc_url,                 # the JDBC URL
               table="training.people_1m",   # the name of the table
               column="id",                  # the name of a column of an integral type that will be used for partitioning
               lowerBound=1,                 # the minimum value of columnName used to decide partition stride
               upperBound=1000000,           # the maximum value of columnName used to decide partition stride
               numPartitions=8,              # the number of partitions/connections
               properties=conn_properties    # the connection properties
              )
         .filter(col("gender") == "M")   # Filter the data by gender
        )

pp_df.explain(True)
```
Note the lack of a **Filter** and the presence of a **PushedFilters** in the **Scan**. The filter operation is pushed to the database and only the matching records are sent to Spark. This can greatly reduce the amount of data that Spark needs to ingest.

### No Predicate Pushdown

In comparison, caching the data before filtering eliminates the possibility for the predicate push down.

```python
cached_df = (spark
            .read
            .jdbc(url=jdbc_url,
                  table="training.people_1m",
                  column="id",
                  lowerBound=1,
                  upperBound=1000000,
                  numPartitions=8,
                  properties=conn_properties
                 )
            )

cached_df.cache()
filtered_df = cached_df.filter(col("gender") == "M")

filtered_df.explain(True)
```

In addition to the **Scan** (the JDBC read) we saw in the previous example, here we also see the **InMemoryTableScan** followed by a **Filter** in the explain plan.

This means Spark had to read ALL the data from the database and cache it, and then scan it in cache to find the records matching the filter condition.

Remember to clean up after ourselves!

```python
cached_df.unpersist()
```