---
id: 9u5zpbod1bvk24cs3u48t3r
title: 11_InstallingMultiNodeCluster
desc: ''
updated: 1699880783825
created: 1699880762043
---

Welcome back!

In this lecture, we are going to set up a three-node Kafka cluster on your local machine.

We'll be using the Confluent Community Edition for this demonstration.

However, the same steps apply to Kafka

Open source as well.

So, let's start

I'm assuming that you have already downloaded an uncompressed your Kafka binaries. And you are not running

any Kafka services.

Go to the Kafka home directory and look for the Kafka server.properties file. If you are using Confluent

Kafka,

then you can find it in etc/kafka directory.

In the case of open-source Kafka, you should see it in the config directory.

Here it is.

This is the Kafka brokered configuration file.

When you start the Kafka broker, you are going to supply this file as an argument. And the Kafka-server-start

command is going to read broker configurations from this file.

We are planning to start three brokers, so let us make three copies of this file and give a different

name to all of them.

Good.

So now we have three files. And when we start the three Kafka brokers, we are going to give the first

file to the first broker,

second file to the second broker, and so on. But before that, we need to modify these files for each broker.

Let us do it.

Let me open the first file.

Here it is.

So Kafka is a highly configurable system. And you can define many of those configurations in this server

properties file.

Some of those are already set in this pre configured default file. For example, the first configuration

is the broker id. Each broker should have a unique id, and you can define the idea in this config

file.

But we leave this configuration unchanged for the first broker.

So the first broker will get an ID zero.

However, since the ID should be unique,

we need to modify the second and the third files and give them a different ID.

Let's do it. Open the second file and change the ID as 1.

Similarly, we change the id as 2 in the third file. Good.

The next most critical property is the listener port the port config is commented, and hence the broker

gets a default port.

This is the port number at which the producers are going to send data to the Kafka broker.

Similarly, consumers are also going to request the data using this port.

The point is a straight. Kafka broker listens to this port for produce and consumes requests coming from

the producer and the consumer.

We are going to run three brokers on this same machine. So, all three brokers should be listening to different

ports.

Hence we uncomment

this configuration and change it. For the first broker, we will leave the default value 9092. But for the

second broker, we will replace it for 9093 similarly for the third broker

we are changing it to 9094.

Now all three brokers will be listening to three different ports, and when we start them, they won't get

a port already in use

error, right? But in a real life scenario, you will be launching one broker on one machine and other

broker on a different computer.

In that case, we do not need to change the port numbers. But running them on a single device would need

to assign different ports. Good! The third configuration is the Kafka log directory location.

This is the directory location where Kafka is going to store the partition data. When you are running

multiple brokers on the same machine,

you should also assign a different directory to each broker.

Let me change this to Kafka-

-logs-0. Similarly, we change it for the second broker and the third broker.

We have many more configurations defined in this file.

However there are not conflicting with each other, and we leave the default values.

You learn more about these configurations in the later sections.

However in the current scenario, we had only three conflicting conflicts.

The broker ID, the listener port, and the log file location.

So we changed all of these to make sure they do not conflict when we are running three brokers on a

single machine.

When you want to run one broker per machine, then you do not need to change anything except to broker

id.

But you can avoid that also by configuring Kafka to auto assign an identifier to each broker.

Yes, that's possible. But we save it for another lesson.

Great!

Now we are all set to start a Kafka cluster of three brokers on a single machine.

Let's do it. The steps are the same as you have done it for a single broker.

The first step is to start the zookeeper server, because Kafka needs zookeeper.

The second step is to start a Kafka broker.

Then you repeat the second step

twice, and that's all.

Let me show you. Start a

command window and start the zookeeper.

You already learn the command, right?

But wait a minute. Before we start,

the zookeeper,

I want to clean up the data directories.

Why?

Because I created a single node Kafka broker, sent some data, did few things for the earlier lecture,

Right? Now I want to start fresh. And the most straightforward method to do that is to delete the Kafka

log directory and the zookeeper data directory.

Where is the data directory?

You already know that, right?

You have seen that in the server.properties file and modified it also. Go to the directory and delete

everything.

Be careful, cleaning this directory will delete all the past data.

But that is what I wanted, right?

Great.

Let's start the zookeeper.

Good.

Now let me open a new command window and start the first broker.

Again the command is the same. But I need to change the config file name.

Done.

Now you're going to start the second broker. And you already know that we prepared a separate config

file for the second broker.

Similarly, you can start the third broker.

You can run as many brokers as you want on a single machine.

All you need to do is to create a separate config file for each broker and start it.

Great!

That's all for this lecture.

See you again in the next video.

Keep learning and keep growing.