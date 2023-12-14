---
id: atbvji7pvjvzef3oao5dusa
title: 12_UsingConsumerGroups
desc: ''
updated: 1699880822910
created: 1699880799042
---
Welcome back.

In the earlier lecture, we created a three-node Kafka cluster.

In this lecture, we are going to use it and do the following things. We will create a new Kafka topic and

make sure that we have three partitions.

Then we are going to start to consumers in this same group.

Both of these consumers should be reading data from the same topic.

However, since they are running in the same group, they should share the workload.

We will see this happening.

Finally, we will start the producer and send the data file to the Kafka cluster. And we will come back

to the consumers and observe the outcome.

Let's start.

Make sure you have your three note Kafka cluster in running state.

Start a new command window.

I'm going to create a new topic. And you already learned the command, right?

I'm going to change the topic name and increase the number of partitions.

Let's run it.

Good.

Now you can start your first consumer and you already learned the command.

Right?

So I'm going to start to console consumer who is going to connect to the Kafka cluster using one of

the brokers.

You do not need to tell all the broker coordinates. Just one is more than enough. Once connected to the

cluster,

my console consumer is going to be reading data from this topic from the beginning. Right?

I'm also going to make this consumer join a group.

Let's name it group1. You can use any string name for your group.

Now I'm going to start a new command window and run one more consumer.

The command remains the same.

Great.

We are all set.

We have a three node Kafka cluster.

We have a topic with three partitions.

We have two consumers running in a group and waiting to read data from these partitions. But we are not

seeing anything because we do not have data yet.

Now I'm going to start a producer and send one data file.

The data goes to the Kafka topic.

Since the topic is partitioned all the data will be distributed among the three partitions.

Some records will come to the first broker in the first partition.

Some of them will go to the other two brokers, and hence they will land in the other two partitions.

However, we have to consumers in the group, right? Three partitions but two consumers.

So, one of these consumers is going to read data from two partitions. And the other one is going to read

the data from the other remaining partition.

However, all the data is processed. Makes sense. Let's try it.

You already know the command. Make sure you are pushing the data to the same topic which these two consumers

are waiting to read. Hit the enter.

Amazing!

You can see the consumers in action. Both of them, right? Who processes how many records?

Here it is.

So this guy got 1224 records and the other one got 663.

Right?

But how do we know how this all happened?

Let me show you.

So I send the data from this file.

This file has got 1907 records. And this is equal to the sum of these two, right?

Now let's go to the log directory

Great!

Looks familiar.

The Kafka Log 0 is the data directory of broker 0, and this one is the data directory of the broker 1.

We defined it in the configuration file, right?

Remember the previous lecture.

Good.

Let's see what we have inside it.

A lot of things. But this is the topic directory.

Right?

This is the topic name we created. But what is this 1 in the directory name?

This is the partition number.

We created three partitions for this topic, right?

So, Kafka is going to name them as partition 0, partition 1 and partition 2. What does it mean?

The stock-ticks-1 is the home of the partition one.

Let me summarize it.

So, we created three brokers, and they all have their own personal data directories, right?

Then we created a stock-ticks topic with three partitions. And these partitions are stored at three different

brokers. Make sense? So we are now seeing the partition one.

Let me get in further.

Here is the data file.

This is these so-called Kafka log file where Kafka would store all the partition data.

Let us dump it.

I want to see what is there inside this log file. And we have a tool to do that. Kafka dump log and

the files to dump.

What do you see? Base offset, last offset, and the total records. And then the next packet. Then the next

packet and so on.

So I have a total of 591 records in this partition. The record counter starts from

offset 0 and goes up to 590, right? Let me dump the other partition logs.

Partition 0 has got 653 records.

And the partition 2 has got 663 records. A log dump is a rich tool. You can even print

the whole data set. But my job is done. Now I know which partition has got what? And I can also make

a guess that the first consumer got these two partitions and processed one thousand two hundred forty

four records. The second consumer has got a single partition and processed 663 records.

Make sense?

So what we did in this lecture. We learned that the Kafka cluster will store data in the topic partitions.

Each partition is managed by a separate broker as a storage directory, and the actual data sets inside

the log files. You can use the log dump tool to investigate these log files.

The next thing that we learn is this - Consumers can work in consumer group to share the work load and

try to achieve workload balance to the extent it is possible.

The main objective of this demo was to give you an insight into how partitions and consumer groups are

used. Running on a multi node cluster was just a bonus that you got. Great!

See you again. Keep learning and keep growing.