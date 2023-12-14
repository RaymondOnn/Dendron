---
id: ou60cbnwsyqdkgdm8tcr5ao
title: jars
desc: ''
updated: 1700801766478
created: 1697930458189
---

### [Setting up dependencies](https://blog.devgenius.io/spark-installing-external-packages-2e752923392e)

- Spark SQL and kafka integration is offered by a seperate package, [spark-sql-kafka`](https://mvnrepository.com/artifact/org.apache.spark/spark-sql-kafka-0-10)

- in /conf/spark.defaults.conf file, add `spark.jars.packages
   <groupId>:<artifactId>:<version>,...`
- for e.g. based on the screenshot, `spark.jars.packages     org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0`
  ![Alt text](spark_streaming_maven_coordinates.png)
- Spark will read this config file and download the required package and related dependencies
> NOTE: No spaces between the package names!! 
- Alternatively, we can do it via code. However, this is not recommended as it's better to keep it outside of application code
  - NOTE: Sometimes this method gets ignored and doesn't work. See [here](https://stackoverflow.com/questions/62106554/why-does-spark-submit-ignore-the-package-that-i-include-as-part-of-the-configura) and [here](https://issues.apache.org/jira/browse/SPARK-21752)

  ```py
  if __name__ == "__main__":
    spark = SparkSession \
        .builder \
        .appName("File Streaming Demo") \
        .master("local[3]") \
        .config("spark.streaming.stopGracefullyOnShutdown", "true") \
        .config("spark.jars.packages", "org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0") \
        .getOrCreate()
  ```
