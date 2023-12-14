---
id: f57o19u2ojqj6n2wtk9g9b6
title: 36_ProducerIOThreadandRetries
desc: ''
updated: 1699882146208
created: 1699882077710
---
Welcome back.

In this video I will talk about the producer retries which is performed by the background I/O thread.

The producer background I/O thread is responsible for transmitting the serialized messages that,

are waiting in the topic partition buffer. When the broker receives the message, it sends back an acknowledgment.

If the message is successfully written to Kafka, the broker will return a success acknowledgement. If

the broker failed to write the message, it would return an error. When the background I/O thread receives

an error or does not receive an acknowledgment, it may retry sending the message a few more times

before giving up and throwing back an error.

You can control the number of read drives by setting the retries producer configuration. When all

the retries are failed, the I/O thread will return the error to the send() method. Great.

See you again keep learning and keep going.