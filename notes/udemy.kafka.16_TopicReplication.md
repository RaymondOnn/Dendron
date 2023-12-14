---
id: q64e8pu4w1uatsgn168i4qs
title: 16_TopicReplication
desc: ''
updated: 1699881299788
created: 1699881061814
---
Welcome back.

Now the next thing that I want to talk about is the Replication factor.

What is the replication factor?

The replication factor specifies how many copies do you want to maintain for each partition. That simply

means the replication factor multiplies to the number of partitions.

For example, we created one Topic for invoices. While creating the Topic, we specified the number of

partitions as five and a replication factor as three.

In this case, Kafka should create 15 directory.

The calculation is simple.

We wanted to produce five partitions and maintain three copies of each partition,

And that makes 15 directories. But where are they?

I showed you only 5 directories.

Where are the other 10 directories? They are created

on the other brokers.

So if you look at the other two broker homes, they also have those five directories. Altogether,

we have five partitions on each broker, and in total, we have 15 directories. We term these directories

as a partition replica.

How do I name them?

Well, I can call it as - the First replica of partition 0. Second replica of partition 0, third replica of partition

0.

Similarly, we have three replicas of partition 1 and so on.

Great!

So we understand that the number of partitions and replication factor multiplies and results into

directories. And we call them as partition replicas.

In our example, we have 15 partition replicas. All these 15 directories are part of the same Topic, but

they're distributed among the available brokers.

Great!

See you again.

Keep learning and keep growing.