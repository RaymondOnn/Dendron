---
id: ltery5a9m7z7hjgq6zdkuyt
title: 10_UsingCliProducerAndConsumer
desc: ''
updated: 1699880741863
created: 1699880730660
---

Welcome back.

In this lecture, we are going to see a Kafka producer and a consumer in action.

I'm not going to write a Kafka producer or a consumer application. Instead, for this first producer/consumer

demo,

we are going to use an out of the box console-producer tool and a console-consumer tool.

So let's get this started.

So, we want to do the following things in this demo.

I have a data file. It is a CSV file that contains some data. And I want to send it to the Kafka cluster.

However a producer should always send data to a Kafka topic.

So we will do it in two steps.

The first step is to create a topic. And we will be using the Kafka-topics command-line tool for

creating it.

Then as a second step, we will be sending all the data from the file to the Kafka cluster. And we will

be using the Kafka console producer tool for doing this.

Once the data starts coming to the Kafka, we want to move to the next step. The next step is to consume

the data from the Kafka topic.

So we will be running a Kafka console-consumer tool to read all the data from the Kafka topic and display

it to the console.

Great.

Let's do it.

We are going to be using the Kafka topics tool

for creating a topic.

The name of the topic is "test," right?

While creating a topic, you should also define the number of participants in the topic.

But the question is this. How many partitions? Well, there are two evaluations. Storage requirement, and Parallel

processing requirement.

I'm going to send a small data set for this topic. So we are not going to consume a lot of space,

and the entire data will easily fit into a single broker.

So, there is no storage concern.

I'll be reading this topic using a single consumer. That is all.

So, there is no parallel processing requirement.

The point is this straight.

A single partition will do the job.

Now the next mandatory thing is to specify the replication-factor.

The replication-factor is the number of copies of each partition.

You can create two or three copies giving a higher replication factor. Why multiple copies?

Well, that's a fault tolerance feature.

The copies are stored at different brokers. And if one broker is down, then we will have another broker

with a copy of the partition.

So, your consumers can still read the data from the cluster

even if one or two brokers are down.

However, in our case, we have a single broker, and it doesn't make any sense to have two or three copies.

So let me set the replication factor as one.

Finally, we are going to create a topic in the cluster, right?

So we need to tell the cluster coordinates.

This command takes the cluster coordinates as bootstrap-server, and the value is the IP and the port.

The IP is the Kafka broker IP or hostname. And the port is the Kafka broker listener port. And 9092

is the default value.

Let me run it. Then the topic is created.

Now we are ready to send the file data.

We are going to use Kafka-

Console-producer to send data to the topic named "test."

We also need to give the cluster coordinates. And for this tool, it is provided as a broker-list.

The value is the same.

Kafka broker hostname or IP and the listener port.

Unfortunately, the two commands would call the same thing differently.

The topic tool calls it bootstrap-server, and the producer calls it the broker-list. But the value is

the same. Anyways, the Kafka-console-producer can take the inputs from a file. So, we are going to redirect

the content of the data file.

Good.

The final step.

We are going to use the Kafka-console-consumer tool to read the same topic from the same cluster.

But we want to read the topic from the beginning.

Let's run it.

Good.

You've got all the records reaching the consumer. If you send one more file.

It will immediately come here.

So, what we did?

We simulated the following.

We have a Kafka cluster in the center.

It was a single node Kafka cluster, but it could have been a 50 node cluster in more complex scenario.

Right?

We had some data files on a remote machine.

Well, we did it on the same computer. But it could have been a remote device that is too far from the

cluster and connected over TCP/IP network.

We sent those files to the Kafka broker, and we use them off-the-shelf Kafka producer application.

We could have written a custom Kafka producer, but Kafka console producer is good at sending data files.

So there was no need to create a new producer.

On the other side, we had a consumer application that was reading the data and processing it. Well, the

processing was too simple. Just displaying it on the console.

But you can have a better Kafka consumer doing some meaningful things, right?

This consumer was also sipping on this same machine. But this guy may also have been away from broker

reading data over TCP/IP network.

Right?

That's all for this lecture.

See you again in the next video.

Keep learning and keep growing.