---
id: ks446c9tbc2ozzupfzpsl76
title: 06_WhenToUseWhat
desc: ''
updated: 1699880560654
created: 1699880526399
---


Welcome back to Kafka Big picture for beginners.

So you are now familiar with the Kafka ecosystem and learned some basics about all five components of Kafka.

The next question is this - When to use what?

And what kind of solution is being developed using the Kafka ecosystem components.

Basically, we see three patterns.

Data integration pattern

Micro service architecture for stream processing

Real-Time Streaming in Data Warehouse and Data Lakes

The first pattern is to focus on your data integration problem and solve it using Apache Kafka.

In these scenarios, you may have a bunch of independent systems.

They serve a specific purpose: generate data, store it, and own it.

However, they also need to send or share some parts of the data with other systems.

So if you are solving a data integration problem using Kafka,

you will use a combination of the first three Kafka components. 

Kafka broker, Kafka Client API, and Kafka connect. 

The Kafka broker is going to give you a shared bus-like infrastructure. 

Whoever wants to share data with other systems will be sending data to the Kafka Brokers.

All other parties interested in that data will consume from the Kafka broker. 

And this architecture gives you the following advantages. 

Suppose you are working with custom or bespoke in-house applications. 

In that case, you will use Kafka client APIs and implement embedded Kafka producers and consumers.

However, if you are working with standard commercial-off-the-shelf(COTS) products, you will use Kafka connect.

What if you cannot find an off-the-shelf Kafka connect connector for one of the COTS applications?

You should use Kafka connect framework to create one rather than raw producer/consumer API.

The second and the most prevailing pattern is to create a real-time stream processing application

adopting a microservice architecture.

You will use a combination of the following Kafka components in these applications. 

Broker, Client APIs only producers, and Kafka streams.

Kafka broker has the same role in these solutions as well. 

Providing a backbone infrastructure and making data available to all your microservices.

These solutions could be using Kafka for two purposes.

Creating streams and Processing streams.

You would be using Kafka producers for creating streams.

However, if you need to create a stream sourced from some COTS applications, you might be using Kafka connect.

But Kafka connect may not apply to the microservices,

and all of them will use embedded Kafka producers to share data with others.

Similarly, you will use Kafka streams to implement your business logic

and achieve your real-time stream processing needs. 

Kafka Consumers are not fit for that purpose 

and lack most of the capability to handle stream processing requirements. 

And also, everything that you can do with Kafka consumers is doable with Kafka streams more efficiently.

So you are not likely to use Kafka consumers.

The third pattern is a relatively new architectural pattern.

And this one is specifically interesting for Data Engineers.

The idea is to implement real-time stream processing in a data warehouse or Data Lake.

So in this pattern, you will collect data from a bunch of source systems into a Kafka cluster.

Once your data starts coming to the Kafka cluster, you will sink all that data into your data lake.

That's all.

Kafka ends there for Data Enginnering.

Once your data is in the data lake, you can apply batch processing or stream processing to this data.

This is the area where Data engineers are interested.

However, KSQL offers to use the Kafka cluster as a data warehouse. 

In this pattern, you'll write queries for your reports using KSQL and run them on the KSQL server.

The KSQL will generate a real-time summary that keeps refreshing every minute, second or millisecond.

This notion of using the Kafka cluster as a database is relatively new and evolving. 

It is not yet widely adopted.

Among all these three patterns, the first pattern is the core of Kafka's capabilities.

It is widely adopted for moving and sharing data amongst various systems.

The second pattern is also popular

and widely implemented in microservice architectures for real-time data handling.

The third pattern is an extension of the first pattern.

Kafka is only required to bring data to the lakehouse in real time.

And rest of the stream processing work is done in the lakehouse using Spark Structured Streaming.

So, If you are a data ingestion or data integration guy, your Kafka focus should be on the following things.

Kafka Broker and Internals.

Kafka Connect and commonly used connectors.

Kafka Connect and commonly used connectors.

Kafka Broker and Internals

Kafka Client APIs

and Kafka Streams

If you are a Data Engineer, your Kafka Focus should be on the following things.

Kafka Broker and Internals.

Interacting with Kafka using Spark Structured Streaming.

Great! That's all for this lecture.

See you again.

Keep learning and keep growing.