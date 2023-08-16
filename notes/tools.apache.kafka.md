---
id: of71gv186953yjr2hrqs4pc
title: Kafka
desc: ''
updated: 1692124538920
created: 1691444711422
---
- Docs for `kafka-python`: https://kafka-python.readthedocs.io/en/master/index.html
- AVRO for Kafka: https://www.confluent.io/blog/avro-kafka-data/
- Kafka Internals: https://www.youtube.com/watch?v=d89W_GzWnRw&pp=ygUTZGlzdHJpYnV0ZWQgc3lzdGVtcw%3D%3D&ab_channel=TheGeekNarrator

## What is Kafka
- Apache Kafka is an event streaming platform used to collect, process, store, and integrate data at scale
- In essence, Kafka is an log file that is append-only and immutable
  - Kafka models **events** as key/value pairs.
  - A **topic** is a log of events and is how Kafka organizes different kinds of events
  - New events are added at the end of the log and cannot be modified (immutable)
  - Reading events is based on using an arbitary offsets 
  - Storage is done through files stored on disk.
  - Every topic can be configured to expire data after it has reached a certain age (or the topic overall has reached a certain size)

```py
# python package for kafka
pip install kafka-python

# Other kafka packages
confluent kafka
pykafka
```


## Partitioning of topics
- Partitioning takes the single topic log and breaks it into multiple logs, each of which can live on a separate node in the Kafka cluster. 
- This way, the work of storing messages, writing new messages, and processing existing messages can be split among many nodes in the cluster and allowing for scaling

### Distribution among partitions
Having broken a topic up into partitions, we need a way of deciding which messages to write to which partitions. 
- Typically, if a message has no key, subsequent messages will be distributed round-robin among all the topic’s partitions. In this case, all partitions get an even share of the data, but we don’t preserve any kind of ordering of the input messages. 
- If the message does have a key, then the destination partition will be computed from a hash of the key. 
- This allows Kafka to guarantee that messages having the same key always land in the same partition, and therefore are always in order.
- Note: Ordering of keys is only guaranteed within the same partition

```py
# creating topic with 2 partitions
 from kafka.admin import KafkaAdminClient, NewTopic

 admin = KafkaAdminClient(
    client_id = 'admin', 
    ...
 )

topic_name_partitioned = TOPIC_NAME + '_partitioned'
topic = NewTopic(
    name = topic_name_partitioned,
    num_partitions = 2,
    replication_factor=3
)
admin.create_topics([topic], timeout_ms = int(<TIMEOUT_MS>)
```

## The Brokers
- From a physical infrastructure standpoint, Kafka is composed of a network of machines called brokers.  
- Each broker hosts some set of partitions and handles incoming requests to write new events to those partitions or read events from them. 
- Brokers also handle replication of partitions between each other.
  - Regardless, brokers are susceptible to failure
  - Hence, to ensure the data is safe, partition data is replicated to several other brokers 
  - Those copies are called follower replica, whereas the main partition is called the leader replica
  - Reading and writing are done to the leader and then replicated to the followers
  - This way, if one node in the cluster dies, another will take over its role.

<br>

## The Producer: Writing to Kafka 
docs: https://kafka-python.readthedocs.io/en/master/apidoc/KafkaProducer.html
- sends events to Kafka broker
- is responsible for:
  - Partition assignment
    - Default: Same key to same partition, no key assign random partition
  - Batching events for improved throughput
  - Compress data before sending
  - Retries
  - Response Callbacks
  - Transaction Handling

```py
# python package for kafka
pip install kafka-python

# create a Producer
from kafka import KafkaProducer

producer = KafkaProducer(
    bootstrap_servers='localhost:9092'
)

# send events
response = producer.send(
    topic_name,
    key= {'id': 1},
    value = {
        'name': 'Francesco'
        'pizza': 'Margerita'
    }
)

# print('is_done', response.is_done)
# print('failed', response.failed())
# print('succeeded', response.succeeded())

producer.flush()


```

## The Consumer: Reading from Kafka
- To consume messages, you'll need to connect to the cluster (passing a configuration map to specify the address of the cluster, security, and other parameters). 
- Then you use that connection to subscribe to one or more topics. 
- When messages are available on those topics, they come back in a collection called ConsumerRecords
  - A ConsumerRecord object represents the key/value pair of a single Kafka message.
- Note that reading a message does not destroy it
- it is still there to be read by any other consumer that might be interested in it. 
- Also, consumers need to be able to handle the scenario in which the rate of message consumption from a topic combined with the computational cost of processing a single message are together too high for a single instance of the application to keep up. That is, consumers need to scale. In Kafka, scaling consumer groups is more or less automatic.



```py
# create a Producer
from kafka import KafkaConsumer

# auto_offset_reset='earliest' & enable_auto_commit=True allows consumer to continue from where it failed
consumer = KafkaConsumer(
    topic=<TOPIC_NAME>,
    bootstrap_servers='localhost:9092',
    auto_offset_reset='earliest',  # read from latest committed offset
    enable_auto_commit=True,  
    group_id='my_consumer_group',
    value_deserializer=lambda x: loads(x.decode('utf-8')))
)

# check out topics
consumer.topics()

# subscribe to topic
# NOTE: listening only starts upon subscription (default behaviour)
consumer.subscribe(topics=[<TOPIC_NAME>])
consumer.subscriptions()

for message in consumer:
    print(
        message.partition,
        message.offset,
        message.key,
        message.value
    )
```

### Consumer Groups
- Consumer groups allow Kafka consumers to work together and process events from a topic in parallel. 
- Each consumer can be assigned a subset of partitions from a topic or set of topics and can parallelize the processing of those events. (Alternatively, consumers can work individually to consume the same stream of events and process those events in different ways.)
- When consumers are added or removed from a consumer group—either because of a changing workload requirement, a rolling machine, or a consumer failure—the Kafka brokers will redistribute the topic-partitions across the new or remaining active members within the group.

#### Creating consumer groups
- To start using consumer groups, just give multiple consumers the same group.id. The group.id can use any string you like, and all the consumers with the same string will be in the same group.
- For example, you might have 5 consumers all running with group.id=invoice-emails, and they will work-share; at the same time, a single consumer can read the same events from the same topic with group.id=sales-analytics and it will work completely independely.


## Kafka Connect

```py
# define schema of message
key_schema = {
    'type': 'struct',
    'fields' : [
        {
            'type': 'int32',
            'optional': False,
            'field': 'id'
        }
    ]
}

value_schema = {
    'type': 'struct',
    'fields' : [
        {
            'type': 'string',
            'optional': False,
            'field': 'name'
        },
        {
            'type': 'string',
            'optional': False,
            'field': 'pizza'
        }
    ]
}

# send some data
producer.send(
    topic_name= <TOPIC_NAME>,
    key = {
        'schema': key_schema,
        'payload': {'id': 1}
    },
    value = {
        'schema': value_schema,
        'payload': {'name': 'Frank', 'pizza': 'Mushrooms'}
    },
)
producer.flush()
```
