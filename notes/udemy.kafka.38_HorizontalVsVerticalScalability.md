---
id: qtj378tmeiw4lajdz6xs0tv
title: 38_HorizontalVsVerticalScalability
desc: ''
updated: 1699882762663
created: 1699882362758
---
Welcome Back!

In the earlier lectures,

We created a very simple producer

and sent one million short messages to the Kafka Server.

But Kafka is all about scalability.

In this lecture,

we will explore some details about scaling up the producers.

So,

let's start.

Apache Kafka was designed with the scalability in mind,

and scaling a Kafka application is straightforward.

If you consider the POS example,

then each POS system can create a KafkaProducer object

and send the invoices.

The Figure here shows the scenario

where multiple POS systems can send invoices in parallel.

At the cluster end,

it is the Kafka broker

that receives the messages

and acknowledges the successful receipt of the message.

So,

if you have hundreds of producers sending messages in parallel,

you may want to increase the number of brokers in your Kafka cluster.

A single Kafka broker can handle hundreds of messages

or maybe thousands of messages per second.

However,

you can increase the number of Kafka brokers in your cluster

and support hundreds of thousands of messages to be received and acknowledged.

On the producer side,

you can keep adding the new producers to send the messages to the Kafka server in parallel.

This arrangement provides linear scalability by merely adding more producers and brokers.

This approach works perfectly for scaling up your overall streaming bandwidth.

However,

you also have an opportunity to scale an individual producer using multithreading technique.

A single producer thread is good enough

to support the use cases

where the data is being produced at a reasonable pace.

However,

some scenarios may require parallelism

at the individual producer level as well.

You can handle such requirements using multithreaded Kafka producer.

The multithreading scenario

may not apply to applications that do not frequently generate new messages.

For example,

an individual POS application would be producing an invoice every 2-3 minutes.

In that case,

a single thread is more than enough to send the invoices to the Kafka cluster.

However,

if you have an application that generates

or receives data at high speed

and wants to send it as quickly as possible,

you might want to implement a multithreaded application.

Great! See you again.

Keep Learning and Keep Growing.