---
id: zpy9gtoikmjxjrczfc9xo3g
title: 31_ProducerRecord
desc: ''
updated: 1699882247390
created: 1699881944837
---
Welcome back.

In the earlier lecture,we have learned some basics of Kafka producer.

Now it is time to deep dive into the producer APIs.

Kafka

producer APIs are straightforward.

You can create a producer by setting some essential configurations and start sending messages using

the send() method.

There is only one restriction that you must package your message in a ProducerRecord object.While

the send() method is a straightforward,

A lot of things happen behind the scenes.

Let's try to understand it in detail.

We package the message content in the ProducerRecord object with at least two mandatory arguments.

Kafka topic name, and message value. Kafka topic name is the destination address of the message.

The message value is the main content of the message.

Right? Other than these two mandatory arguments,

You can also specify the following optional items.Message key,target partition,message timestamp.The

message key is one of the most critical argument,and it is used for many purposes,such as partitioning

grouping, and joins.

You will learn more about the usage of a key as you progress with the course.

However,at this stage,consider it as another mandatory argument

even if the API doesn't mandate it. Target partition and the timestamp are purely optional, and you may

want to set these arguments rarely. I'll talk about these arguments in a minute.

The ProducerRecord wraps your message content with all necessary information such as topic name,

Key, and timestamp.Once created,

you can hand over the ProducerRecord to the KafkaProducer using the send() method.

Great.

See you again.

Keep learning and keep going.

