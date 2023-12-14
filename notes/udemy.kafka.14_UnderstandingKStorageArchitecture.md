---
id: xn7kp9b8uk774lxyyx2vys8
title: 14_UnderstandingKStorageArchitecture
desc: ''
updated: 1699881003110
created: 1699880958464
---
Welcome back.

In this section, we start exploring Kafka internals and some architectural concepts.

These fundamental concepts are critical for the rest of the course.

Great.

Let's start. What is a Apache Kafka?

Think about it.

You must be categorically sure about the answer.

Apache Kafka is a messaging broker.

That is all,

everything else is an API, library or a framework to either interact with the Kafka broker or work with

the data in real-time.

Kafka broker is a middleman between producers and consumers, and it plays three primary responsibilities.

Receive messages from the producers and acknowledge the successful receipt. Store

the message in a log file to safeguard it from potential loss. Storage is also critical to ensure

that the consumers can consume it later, and they do not necessarily need to read it in real-time.

Finally, deliver the messages to the consumers when they request it.

That's all at a high level.

That is what Kafka broker does. However,

that's an oversimplified answer to the question about Kafka.

A more elaborated answer could be something like this.

Apache Kafka is a horizontally scalable, fault-tolerant, distributed streaming platform, and it is consciously

designed for building real-time streaming data architecture.

This new definition is a lot to understand.

So, I break things into three parts to make it easy to understand. In the first part,

I will talk about Kafka message storage architecture.

This discussion will help you to understand some core concepts such as Kafka topics, logs, partitions,

replication factor, segments, offsets, and offset-index.

Then we will move into Kafka cluster architecture.

This discussion will help you understand some concepts associated with Cluster formation, Zookeeper,

and the Controller.

Finally, will tie up these two architectures and try to understand how the work is distributed in the

Kafka cluster.

In this part, you will learn some concepts such as Leaders, Followers, In Sync Replicas, committed and uncommitted

messages.

Great!

That's all for this video. In the next video, we will learn about Kafka storage architecture.

See you in the next lecture.

Keep learning and keep growing.