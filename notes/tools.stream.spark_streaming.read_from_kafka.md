---
id: 54o8i5avq7uoq0sgest2etx
title: read_from_kafka
desc: ''
updated: 1696734429769
created: 1696732477287
---
<https://medium.com/plumbersofdatascience/kafka-with-spark-streaming-different-approaches-to-read-data-f38616c023b8>

## Reading data from kafka

- Direct Stream Approach
- Receiver-based Approach
- Structured Streaming Approach

## Direct Stream Approach

- This approach uses the `createDirectStream()` method from the `pyspark.streaming.kafka` module to read data directly from Kafka.
- This method creates a direct connection between the Spark Streaming application and the Kafka brokers.
- It allows you to consume data from one or more Kafka topics, and provides automatic load balancing and fault tolerance.

``` py
from pyspark.streaming.kafka import KafkaUtils
from pyspark.streaming import StreamingContext

ssc = StreamingContext(spark.sparkContext, 1)

kafkaParams = {
  "bootstrap.servers": "kafka:9092",
  "auto.offset.reset": "smallest"
}

directStream = KafkaUtils.createDirectStream(
  ssc,
  topics=['my_topic'],
  kafkaParams=kafkaParams
)

directStream.print()

ssc.start()
ssc.awaitTermination()
```

### Use Cases

- Real-time processing of high-volume data streams, such as clickstream data, sensor data, or financial market data, where **low latency is critical and a high volume of data needs to be ingested quickly**.
- Example: A financial services company processing a high volume of real-time stock market data for trading strategies and decision-making.
- Example: An energy company processing telemetry data from wind turbines to detect faults and optimize power generation.

## Receiver-based Approach

- This approach uses the `createStream()` method from the `pyspark.streaming.kafka` module to read data from Kafka.
- This method creates a receiver on the Spark worker nodes that receives data from Kafka, and then forwards it to the Spark Streaming application.
- This approach is useful when you want to consume data from multiple Kafka topics, or when you want to use the Kafka high-level consumer API.

``` py
from pyspark.streaming.kafka import KafkaUtils
from pyspark.streaming import StreamingContext

ssc = StreamingContext(spark.sparkContext, 1)

kafkaParams = {
  "zookeeper.connect": "localhost:2181",
  "group.id": "my_group"
}

receiverStream = KafkaUtils.createStream(
  ssc,
  "localhost:2181",
  "my_group",
  {"my_topic": 1}
)

receiverStream.print()

ssc.start()
ssc.awaitTermination()
```

### Use Cases

- Real-time processing of data streams where
  - moderate-to-high throughput is required, and processing can tolerate slightly higher latency OR
  - high throughput is required, but lower latency is acceptable, and data sources have limited partitioning.
- Example: A social media platform processing user-generated content data in real-time for moderation, sentiment analysis, and targeted advertising.
- Example: A video streaming platform processing real-time user engagement data for recommendation algorithms and improving user experience.

## Structured Streaming Approach

- This approach uses the `readStream()` method from the `pyspark.sql.streaming` module to read data from Kafka using Spark's structured streaming API.
- This method creates a streaming DataFrame that reads data from Kafka and allows you to perform SQL-like operations on the data.

```py
from pyspark.sql.functions import from_json, col
from pyspark.sql.types import StructType, StringType, IntegerType

schema = StructType() \
    .add("id", IntegerType()) \
    .add("name", StringType()) \
    .add("age", IntegerType())

df = spark \
  .readStream \
  .format("kafka") \
  .option("kafka.bootstrap.servers", "localhost:9092") \
  .option("subscribe", "my_topic") \
  .load() \
  .selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)") \
  .select(from_json(col("value"), schema).alias("data")) \
  .select("data.*")

df.writeStream \
  .format("console") \
  .option("truncate", "false") \
  .start() \
  .awaitTermination()
```

### Use Cases

- Processing of data streams that require
  - complex analytics, or transformations, and
  - require a high level of fault tolerance and exactly-once semantics.
- Example: A transportation logistics company processing real-time sensor data from vehicles, weather data, and traffic data to optimize delivery routes and improve fleet management.
- Example: A cybersecurity company processing real-time network traffic data to detect and prevent cyber attacks.

## Comparison

- Each approach has its own set of advantages and limitations
- The choice of the best approach for your use case will depend on factors such as
  - the volume, velocity, and complexity of the data
  - the desired level of fault tolerance
  - the reliability of the data processing.

### Direct Stream Approach

``` diff
Advantages
+ Provides low latency data ingestion with minimal overhead and delay
+ Supports consuming data from multiple Kafka topics and partitions
+ Provides automatic load balancing and fault tolerance
+ Suitable for high-volume data streaming applications

Limitations:
- Requires configuration of offsets and partition management
- Limited control over offset management and may require manual intervention in some cases
- Requires continuous and stable connectivity between Spark Streaming application and Kafka brokers
```

### Receiver-based Approach

``` diff
Advantages:
+ Provides better throughput than direct stream approach in some cases
+ Allows for consuming data from multiple Kafka topics and partitions
+ Provides automatic load balancing and fault tolerance
+ Can use the Kafka high-level consumer API

Limitations:
- Can have higher latency compared to direct stream approach
- Requires continuous and stable connectivity between Spark Streaming application and Kafka brokers
- May require manual intervention in some cases, such as handling receiver failures or restarting streaming application
```

### Structured Streaming Approach

``` diff
Advantages:
+ Provides a high-level API for real-time data processing with Spark
+ Supports consuming data from multiple Kafka topics and partitions
+ Provides support for exactly-once semantics with the use of the “checkpoint” option
+ Allows for integration with Spark SQL for complex analytics and data transformations on the data stream

Limitations:
- Can have higher latency compared to direct stream approach or receiver-based approach
- Requires stable and reliable Kafka cluster with appropriate configuration and tuning
-Requires proper management of checkpointing and state management for reliable processing of data
```
