---
id: m9d9lpi6lvjdnp65wymnvru
title: Best_practices
desc: ''
updated: 1697207622020
created: 1697206491165
---

https://blog.clairvoyantsoft.com/productionalizing-spark-streaming-applications-4d1c8711c7b0

### Beginners best practices checklist for Spark Streaming

- Choose a trigger interval over nothing at all because it helps control storage transaction api/Listing costs. This is because some Spark jobs have a component which requires a s3/adls listing operation. If our processing is very fast think <1 sec, we will keep repeating these operations and lead to unintended costs. Example .trigger(processingTime=’5 seconds’)
- If you are using AutoLoader the switch to Notification mode:
   <https://docs.databricks.com/ingestion/auto-loader/file-notification-mode.html>
- Do not enable versioning on the S3 bucket, Delta tables have time travel to recover from failures as Versioning adds significant latency at scale.
- Keep the compute and storage located in the same regions.
- Use ADLS Gen2 on Azure over blob storage as it’s better suited for big data analytics workloads. Read more on the differences here.
- Make sure the table partition strategy is chosen carefully and on low cardinality columns like date , region, country, etc. My rough rule of thumb says, if you have more than 100,000 partitions then you have over-partitioned your table. Date columns make a good partition column because they occur naturally. Example for a multinational e-commerce company which operates in 20 countries and wants to store 10 years of data. Once you partition by date & country =( 365 *10 )* 20 = you will end up with 73,000 partitions.
- Name your streaming query so it is easily identifiable in the Spark UI Streaming tab i.e. `.option(“queryName”, “IngestFromKafka”)`

``` py
(input_stream
   .select(col("eventId").alias("key"), to_json(struct(col('action'), col('time'), col('processingTime'))).alias("value"))
   .writeStream
   .format("kafka")
   .option("kafka.bootstrap.servers", kafka_bootstrap_servers_plaintext )
   .option("kafka.security.protocol", "to_be_filled")
   .option("checkpointLocation", checkpoint_location )
   .option("topic", topic)
   .option("queryName", "IngestFromKafka")
   .start()
)

# --------------------------------------------------------------------

spark.readStream.format(“kinesis”).option(“streamName”, stream_name)
```

- Each stream must have its own checkpoint; streams must never share checkpoints. Example, if you have 2 separate streams of different sources and data needs to be written to a single delta table. You should create 2 separate checkpoints and not share a common one. You can find an example with code [here](https://canadiandataguy.medium.com/merge-multiple-spark-streams-into-a-delta-table-44301fd549bd).
- Don’t run multiple streams on the same driver; if such requirements are there, please benchmark it by running the streams for a few days and watch for stability over driver-related issues. Multiplexing on the same cluster is generally not recommended.
- Partition size of data in memory should be between 100–200MB. Use Spark UI and alter maxFilesPerTrigger & maxBytesPerTrigger to achieve these partition sizes of around 100–200 MB.
- Check if a sort merge join can be changed to Broadcast hash join. Only possible if the dataset being joined is small. The dataset being broadcasted should be around 100 MB. Increase auto-broadcast hash join threshold to 1gb if needed try a bigger instance family.

### Advanced best practices checklist for Spark Streaming

- Establish a naming convention for your checkpoint: Over the course of the life of your table, you will end up having multiple checkpoints due to application upgrades, logic changes, etc. Give your checkpoint a meaningful name something which tells the following:
  - Target table name
  - Starting timestamp: When the checkpoint came into existence
  - Example naming conventions can be :
    1. Generic {table_location}/*checkpoints/*{target_table_name}_starting_timestamp{_actual_timestamp}
    2. If source is Delta then use startingVersion {table_location}/*checkpoints/*{target_table_name}_startingVersion{_startingVersion}
- See Shuffle Spill (Disk) on Spark UI to be as minimum as possible. Ideally zero only shuffle read should be there. Shuffle spill disappears from UI if it’s zero.
- Use rocks DB , if there are stateful transformations.
- Prefer to Azure Event Hub using it it’s Kafka connector. For Azure EventHubs, the number of cores must be == to number of partitions. With Kafka connector it is different, as it can split Kafka partition into multiple Spark partitions, and this is one of the reasons to go with Kafka protocol on EventHubs.
- If there is state always have a watermark so it can clean itself. In case you need to have infinite state, recommend you to store that in a Delta table and zorder on necessary column so lookups are fast.
- At big scale, think close to Trillions of records in state store. If there is a deduplicate requirement, use delta merge approach over drop duplicate to avoid state store growing very large.
- Azure instance family choices:
  - F-series for map-heavy streams — parsing, json deserialization, etc.
  - Fsv2-series if doing multiple streams from the same source or need a little spill space.
  - DS_v2-series for streams that join tables or do aggregations. Also for delta optimize (both bin-packing and Z-Ordering) scheduled jobs
  - L series has direct attached SSD which helps Delta caching
- Don’t set the sql.shuffle.partitions too high — ideally they should be set to be equal to the total number of worker cores. You will need to clear the check point if it is not changing. It is because checkpoint has stored this information and is using that.
