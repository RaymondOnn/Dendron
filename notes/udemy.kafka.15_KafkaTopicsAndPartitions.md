---
id: 2u21nvh1y2tiah3h5bwz3hu
title: 15_KafkaTopicsAndPartitions
desc: ''
updated: 1699881042309
created: 1699881020347
---
Welcome back.

In the earlier lectures you already learned that Apache Kafka organizes the messages in topics, and the

broker creates a log file for each topic to store these messages. However, these log files are partitioned,

replicated and segmented. In this lecture,

I will not only explain but also show you the Kafka log file organization. So, this lecture is going to

be a working session.

Great!

Let's start with a high-level concept of the Topic.

What is a Kafka Topic?

A topic is a logical name to group your messages. Like in a database,

you must create a table to store your data records, in Kafka,

you must create a topic to store messages.

So let's create a topic and see a few things in action.

I have a three node Kafka cluster running on my machine.

I've configured things in a way that I can efficiently work from the IntelliJ IDEA, and I do not

have to switch screens.

I've also set all three brokers to use this temp directory as a home directory.

What does that mean?

That means, everything that my Kafka brokers are going to create, will reside in this tmp directory.

This arrangement makes things easy to watch and understand what is happening under the hood.

My three node Kafka Cluster is up, and all three brokers are running.

Let me show you what do we have inside the temp directory. I have this zookeeper directory.

Kafka needs the zookeeper, and I'll explain it in a different video. However,

for now, you can think of this directory as a zookeeper data directory where the zookeeper will store

its data. Other three directories are the data directories of my three Kafka brokers. Let me take you

inside one of these directories. So these are some initial files. When a broker starts, it creates some

initial files.

Most of these files would be empty at this point.

I haven't created any topic yet, so we do not have any file for a topic.

This is an initial state of the Kafka broker.

We do not want to get into further details of these files.

Our focus is to understand how a topic is organized at the broker level. So, we will create one topic and

investigate these directories to know how a topic is physically held at the broker.

So let's create a topic.

Here is the command that I would use to create a Kafka topic. Kafka topics is a shell script

on Linux machine and a batch script on a Windows machine.

Rest all our arguments to the command.

The first argument is to tell Kafka that we want to create a topic. The second argument is the zookeeper

coordinates. The third argument is the name of the topic. These three arguments are mandatory and fundamental

arguments for the command to work. When you create a topic in Apache Kafka,

you must specify two more configurations. Number of Partitions and Replication Factor.

If you do not provide these parameters, Kafka assumes a default value, but every topic must have some

values for these two parameters. Let's execute this command, and then I'll talk about the partitions and

replication factor. Great! Topic creation is complete.

Now, let me talk about the topic partition.

What are they?

In Kafka,

a single topic may store millions of messages, and hence, it is not practical to keep all those messages

in a single file.

The topic partitions are a mechanism to break the topic further into smaller parts.

Right?

But what does it mean for Apache Kafka? For Apache Kafka, a partition is nothing but a physical directory.

What does it mean?

It means a simple thing. Apache Kafka creates a separate directory for each topic partition. I created

a topic for invoices and specified five partitions, that means, Kafka will create five directories for

the invoice topic.

You can see it here. We got five new directories because we created five partitions for the invoice topic.

So, Kafka topic partitions are nothing but directories. You create a Topic with 3 partitions, Kafka

broker will create three directories. You asked for 10 partitions, the broker will create 10 directories.

Simple isn't it? I hope you got some sense about Kafka Topic partitions. Great!

See you again.

Keep learning and keep going.