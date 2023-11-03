---
id: 80qchg5u0tpr30eymf9ch83
title: kafka
desc: ''
updated: 1697307381090
created: 1697307081035
---

## Implementation with Kafka

- A dead letter queue is a simple topic in the Kafka cluster which acts as the destination for messages that were not able to make it to their desired destination due to some error.
- Surround your kafka consumer logic inside try-block and if any exception occurs send the message to “dlq” topic.

### Use Cases

- A message on “source-topic” was not a valid JSON format so could not be deserialized by the consumer. As the message is not in valid format it cannot be transformed and published to “target-topic”. It’s better to log such malformed messages to a “dlq” target topic from where the malformed messages can be analysed later without interrupting the flow of other valid messages.
- An error occurs while processing a message from the “source-topic”. This might occur when the message is in a valid JSON format but the data is not as expected. The simplest example is if the message has a field for age which is expected to be positive, but we have received “age”: “-30” in the message. Such messages should be logged to “dlq” topic for further analysis.
- “target-topic” is full so cannot accept any new messages. This might happen if the load on your topic is very high. In this case we can have a target “dlq” topic for such messages.
- “target-topic” does not exists. Thus we don’t have any destination for the messages and a possibility of message loss. While this scenario rarely occurs, it’s better to have some target topic for such messages.

## An Example

```py
import json
import traceback
from kafka import KafkaConsumer, KafkaProducer # kafka-python
from config import *

# Creating the Kafka producer for valid messages
target_topic = 'json-topic-2'
json_serializer = lambda x: json.dumps(x).encode('utf-8')
valid_producer = KafkaProducer(bootstrap_servers=[BOOTSTRAP_SERVERS],
                               value_serializer=json_serializer,
                               acks='all')

# Creating the Kafka producer for invalid/malformed messages
dlq_topic = 'json-topic-dlq'
string_serializer = lambda x: x.encode('utf-8')
dlq_producer = KafkaProducer(bootstrap_servers=[BOOTSTRAP_SERVERS],
                             value_serializer=string_serializer,
                             acks='all')

# Creating the Kafka consumer
source_topic = 'json-topic'
consumer = KafkaConsumer(source_topic, group_id='some_consumer_group',
                         bootstrap_servers=[BOOTSTRAP_SERVERS],
                         auto_offset_reset='latest',
                         consumer_timeout_ms=100000)

while True:
    msg_pack = consumer.poll(timeout_ms=500)  # Response format is {TopicPartiton('topic1', 1): [msg1, msg2]}
    for tp, messages in msg_pack.items():
        for message in messages:

            data = message.value.decode('utf-8')
            print("\nConsumed[%s/%d] %d: key=%s value=%s" %       (message.topic, message.partition, message.offset, message.key, message.value))
            try:
                json_data = json.loads(data)

                transformed_employee_number = 'EMP-'+str(json_data['Employee-Number'])
                print("Transforming Employee-Number:", json_data['Employee-Number'],
                      'to Employee-Number:', transformed_employee_number)

                json_data['Employee-Number'] = transformed_employee_number
                print("Sending json data to valid topic: ", json_data)

                valid_producer.send(topic=target_topic, value=json_data).get()
                print("Successfully send json data to valid topic: ", json_data)

            except json.decoder.JSONDecodeError as e:
                print(e.__class__.__name__, e)
                print("Sending malformed json data to DLQ topic: ", data)
                dlq_producer.send(topic=dlq_topic, value=data).get()
                print("Successfully sent malformed json data to DLQ topic: ", data)
```

Consumer  
In the above code, the property ‘group_id’ is essential as it specifies which consumer group the consumer is a member of. The property ‘enable_auto_commit’ is set to ‘False’ to ensure that it doesn’t commit offsets automatically.

```py
# To consume from techexplained-topic
consumer = KafkaConsumer('techexplained-topic',
                        group_id='myGroup', enable_auto_commit=False,
                        bootstrap_servers=['localhost:9092'],
          value_deserializer=lambda m: json.loads(m.decode('ascii')))
for message in consumer:
   print (message.topic)
   print (message.partition)
   print (message.offset)
   print(message.key)
```
