---
id: dvnvqeqhcb5smnxhzjthqtj
title: consumer_lag
desc: ''
updated: 1696744980356
created: 1696734774575
---
<https://medium.com/@dineshkumarkct/consumer-lag-monitoring-for-spark-streaming-application-kafka-5d58d07106d>

## What is Consumer Lag

- When you build a consumer to read messages from a message queue such as Apache Kafka and the consumer consumes and processes slower than the rate of production of messages, it will contribute to Lag.
- For example,
  - if Kafka gets the messages in topic at the rate of 100 messages per second
  - the consumer you built consumes and processes at the rate of 50 messages per second
  - the lag will pile up at the rate of 100–50 = 50 messages per second.
  - So after one hour, the total lag will be 1,80,000 messages.

## Why is Consumer Lag crucial in a streaming pipeline?

- Most of the streaming pipelines we build is to process data in near real time
- when there is a lag the target will start seeing delay in the data synchronisation.

## How common is Lag in a streaming pipeline?

Lag is very common in a streaming pipeline because of the following reasons

- The upstream system can have fluctuations in traffic based on the usage.
- There can be a sudden event by a big customer and this can contribute to a big spike in traffic
- Some instances in the streaming pipeline can be unhealthy for a short period of time and before it recovers, the lag will pile up
Lag is an indicator of the health and scale of the pipeline. When there is a lag and if it is piling up over time, action is needed from the engineers.

## Calculating Consumer Lag
- Kafka offset value can provide useful information on how spark structured streaming app is performing
  - End Offset: Points to the last record appended to a partition.
  - Begin Offset: Points to the oldest readable record in a partition. Message committed into Kafka expires in 7 days by default.
  - Current Offset: Points to the last record consumed by your spark application.



## Lag monitoring system

- A Lag monitoring system to monitor the lag between the consumer and the topic in real time is mandatory.
- Typically, a dashboard that shows the lag for every X minutes is needed to monitor the lag and see if it piles up due to any unfortunate event.
- If the streaming pipeline fails, the lag will continuous grow since no messages are consumed and transformed. This is a significant indicator for the engineers to identify if the pipeline is not running as expected.

### With the Burrow API

#### Monitoring Kafka

- Generally the Kafka will have a Burrow URL associated with it. This will provide the lag number for any consumer group attached to the Kafka broker.

``` json
{
  "error": false,
  "message": "consumer status returned",
  "status": {
    "cluster": "virginia",
    "group": "my_consumer_group",
    "status": "OK",
    "complete": 1,
    "partitions": [
      {
        "topic": "my_topic",
        "partition": 0,
        "owner": "/0.0.0.0",
        "client_id": "kafkaclient",
        "status": "OK",
        "start": {
          "offset": 149887382,
          "timestamp": 1679286242711,
          "observedAt": 1679286252000,
          "lag": 0
        },
        "end": {
          "offset": 149887819,
          "timestamp": 1679286377713,
          "observedAt": 1679286387000,
          "lag": 0
        },
        "current_lag": 0,
        "complete": 1
      },
      {
        "topic": "my_topic",
        "partition": 1,
        "owner": "/0.0.0.0",
        "client_id": "kafkaclient",
        "status": "OK",
        "start": {
          "offset": 149740248,
          "timestamp": 1679286247799,
          "observedAt": 1679286258000,
          "lag": 0
        },
        "end": {
          "offset": 149740615,
          "timestamp": 1679286382801,
          "observedAt": 1679286388000,
          "lag": 0
        },
        "current_lag": 0,
        "complete": 1
      },
      {
        "topic": "my_topic",
        "partition": 2,
        "owner": "/0.0.0.0",
        "client_id": "kafkaclient",
        "status": "OK",
        "start": {
          "offset": 149952545,
          "timestamp": 1679286255168,
          "observedAt": 1679286265000,
          "lag": 400
        },
        "end": {
          "offset": 149952924,
          "timestamp": 1679286390169,
          "observedAt": 1679286390000,
          "lag": 0
        },
        "current_lag": 400,
        "complete": 1
      }
    ],
    "partition_count": 2,
    "maxlag": {
      "topic": "my_topic",
      "partition": 0,
      "owner": "/0.0.0.0",
      "client_id": "kafkaclient",
      "status": "OK",
      "start": {
        "offset": 149887382,
        "timestamp": 1679286242711,
        "observedAt": 1679286252000,
        "lag": 0
      },
      "end": {
        "offset": 149887819,
        "timestamp": 1679286377713,
        "observedAt": 1679286387000,
        "lag": 600
      },
      "current_lag": 600,
      "complete": 1
    },
    "totallag": 1000
  },
  "request": {
    "url": "/v3/kafka/my_cluster/consumer/my_consumer_group/lag",
    "host": "host-1- virginia"
  }
}
```

#### Monitoring Spark Streaming

- Spark does not have a concept of standard consumer group and it completely depends on Checkpoint file only.
- Checkpoint directory that will get updated between micro-batches
- Offsets directory will have a new file created for every batch to be processed.
  - An Offset file will look like below

    ``` json
    v1

    {
        "batchWatermarkMs": 0,
        "batchTimestampMs": 1679281140002,
        "conf": {
            "spark.sql.streaming.stateStore.providerClass": "org.apache.spark.sql.execution.streaming.state.HDFSBackedStateStoreProvider",
            "spark.sql.streaming.join.stateFormatVersion": "2",
            "spark.sql.streaming.stateStore.compression.codec": "lz4",
            "spark.sql.streaming.stateStore.rocksdb.formatVersion": "5",
            "spark.sql.streaming.statefulOperator.useStrictDistribution": "false",
            "spark.sql.streaming.flatMapGroupsWithState.stateFormatVersion": "2",
            "spark.sql.streaming.multipleWatermarkPolicy": "min",
            "spark.sql.streaming.aggregation.stateFormatVersion": "2",
            "spark.sql.shuffle.partitions": "200"
        }
    }
    {
        "topic_1": {
            "0": 55723268,
            "1": 55874113,
            "2": 56377024
        },
        "topic_2": {
            "0": 6845083,
            "1": 6849802,
            "2": 6846012
        },
        "topic_3": {
            "0": 107977253,
            "1": 107835992,
            "2": 108074880
        }
    }

    ```
- Commits directory will have a file with the same name as Offsets once the batch is complete.
    ``` json
    v1
    {"nextBatchWatermarkMs":0}
    ```
- Thus the Offset file is the source for us to generate the total lag.
- Even-though the Spark Streaming does not register the consumer groups in Kafka, the burrow URL can still return the latest offset for any particular topic using the Topics API
    ``` json
    // response from topics API
    {
        "error": false,
        "message": "topic offsets returned",
        "offsets": [
            400525470,
            400150589,
            400165658,
            313568243,
            313586906,
            313652577,
            42689842,
            42665916,
            42663526,
            42669086,
            42685936,
            42690057
        ],
        "request": {
            "url": "/v3/kafka/my_cluster/topic/my_topic",
            "host": "tools_1"
        }
    }
    ```

#### Calculating the metrics: Consumer Lag
- Latest lag number can be produced by comparing the difference in offsets between the Topic Response and the latest Checkpoint files 

``` py
# Note: S3 as checkpoint location
def get_lag_from_checkpoint(bucket, burrow_url, checkpoint_path):

        application_total_lag = 0

        s3 = boto3.client(‘s3')
        paginator = s3.get_paginator("list_objects")

        def get_last_modified(obj):
            return int(obj[‘LastModified’].strftime(‘%s’))

        page_iterator = paginator.paginate(Bucket=bucket, Prefix="{}/offsets/".format(checkpoint_path))

        last_added = None

        for page in page_iterator:
            if "Contents" in page:
                last_added = [obj[‘Key’] for obj in sorted(page["Contents"], key=get_last_modified)][-1]

        if last_added is None:
            raise Exception

        result = s3.list_objects(Bucket=bucket, Prefix=last_added)
        obj = result.get(‘Contents’)[0]
        data = s3.get_object(Bucket=bucket, Key=obj.get(‘Key’))
        contents = data[‘Body’].read()
        dict_content = json.loads(contents.decode("utf-8").split(‘\n’)[2])
        topics = dict_content.keys()

        for topic in topics:

            topic_total_lag = 0

            url = ‘{}/{}’.format(burrow_url, topic)
       
            try:
                data = get_response_from_url(url)
            except:
                raise Exception

            topic_max_offsets = data['offsets']

            for partition in dict_content[topic]:
                topic_total_lag = topic_total_lag + (
                        topic_max_offsets[int(partition)] - dict_content[topic]['{}'.format(str(partition))])

            application_total_lag = application_total_lag + topic_total_lag

        return application_total_lag if application_total_lag >= 0 else 0
```

### Getting offsets without Burrow API

``` py
# get the latest offset for every poll interval
from kafka import KafkaConsumer, TopicPartition

consumer = KafkaConsumer(
                group_id='',
                bootstrap_servers='',
                sasl_plain_username='',
                sasl_plain_password='',
                security_protocol='',
                sasl_mechanism='',
                ssl_cafile='',
                enable_auto_commit=False
            )

tp = TopicPartition(topic, partition)
consumer.assign([tp])
consumer.seek_to_end(tp)

# Get last offset for the topic:partition
last_offset = consumer.position(tp)
```

### Looking at the metrics

- Consomer lag is calculated for each trigger interval
- Ideally, the number should always be between 0 and Maximum number set that can pile up for my application.
- If it piles up like below, then there is a problem which we need to debug and fix it.
