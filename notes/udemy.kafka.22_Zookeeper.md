---
id: 29ym7u75h7e96x3nwajxjh8
title: 22_Zookeeper
desc: ''
updated: 1699881481856
created: 1699881460612
---
Welcome back. Kafka

Broker is a master less cluster.

It doesn't follow a master slave architecture.

However, it uses Apache ZooKeeper to maintain the list of active brokers.

Every Kafka broker has a unique_id that you define in the broker configuration file.

We also specify the zookeeper connection details in the broker configuration file.

When the broker starts, it connects to the zookeeper and creates an ephemeral node using broker_id to

represent an active broker session. The ephemeral node remains intact as long as the broker session with

the zookeeper is active.

When the broker loses connectivity to the zookeeper for some reason, the zookeeper automatically removes

the ephemeral node. So the list of active brokers in the cluster is maintained as the list of ephemeral

nodes under the /brokers/ids path in the zookeeper. I can show you that list.

You can to start a zookeeper shell using this command.

Great.

I'm connected to the zoo keeper of my Kafka Cluster. I can easily look at what we have in the zookeeper

database. Let me issue the ls command.

Great!

We have these many hierarchies, but I'm interested in the brokers.

So let's check brokers. Okay!

So let's look into the ids.

Great.

So I have 0, 1, and 2. Three brokers in this cluster.

What if I bring one broker

down. Let me stop

broker id 1.

Broker id 1 is gone from the zookeeper.

Let me start

the broker once again.

Great.

So this is how Kafka maintains the list of brokers in a cluster.

The list of active brokers in the cluster is maintained as the list of ephemeral nodes under the /brokers/ids

path in the zookeeper.

Great.

See you again.

Keep learning and keep growing.