---
id: fyi8l086gnfvj9z89uzh7i7
title: 24_PartitionAllocationAndFaultTolerance
desc: ''
updated: 1699881562871
created: 1699881543395
---
Welcome back.

In the earlier session, we explore two different dimensions of Apache Kafka. Log files and Cluster

formation. The next logical step is to tie up the relationship between these two dimensions and understand

how the work is distributed among the brokers in a Kafka cluster. Primarily, we want to understand what

makes Kafka is scalable and far tolerance system.

If we look at the kafka topic organization, it is broken in two independent partitions. Each partition

is self-contained.

What I mean is simple. All the information about the partition, such as segment files and indexes are

stored inside the same directory.

This structure is excellent because it allows us to distribute the work among Kafka brokers in a

cluster efficiently. All we need to do is to spread the responsibility of partitions in the Kafka cluster.

The point is is straightforward. When you create a topic, the responsibility to create, store, and manage

partitions is distributed among the available brokers in the cluster.

That means every Kafka broker in the cluster is responsible for managing one or more partitions that

are assigned to that broker. And that is how the verdict is shared by the brokers.

Having said that, let's try to understand it with the help of an example. Kafka cluster is a group of

brokers.

These brokers may be running on individual machines. In a large production cluster,

you might have organized those machines in multiple racks.

This diagram shows a six-node cluster that is designed using two tracks.

Now I have a simple question.

How are the partitions allocated to brokers?

I mean, how we decide which brokers should be maintaining which partition?

Are there any rules for assigning work among the brokers?

Let me explain this.

Suppose you have six brokers and you placed them into two different racks

as shown in the figure. You decide to create a topic with 10 partitions and a replication factor of three.

That means, Kafka now have 30 replicas to allocate to six brokers.

Are there any rules for the assignment?

Well, Kafka tries to achieve two goals for this partition allocation. Partitions are distributed evenly

as much as possible to achieve work load balance follower partitions I mean duplicate copies must be

placed on different machines to achieve for tolerance. That's it. Just two goals.

These are not rules, but you can pick them as a goal that Kafka would want to achieve.

Now let's see how it gets distributed.

The rule part of it.

Right?

To distribute our 30 partitions, Kafka applies the following is steps. Make an ordered list of available

brokers, and then assign leaders and followers to the list in order.

Let's apply the first rule. Kafka begins with a randomly chosen broker in a rack and places it into a list.

Let's assume we start with broker 0.

Now we have one item on the list.

The next broker in the list must be from a different rack.

So, let's assume Kafka picks

Broker 3.

Now we have two brokers in the ordered list.

The next one again comes from the first track.

Let's say, this time it is broker 1.

This goes on as an alternating process for selecting another broker in a different rack.

So, we take broker 4, then broker 2, and finally broker 5.

That's all. You have an ordered list of brokers.

Now we come to the second step. Assigning partitions to this list.

We have 30 partitions to allocate to these six brokers.

Ideally, Kafka should place five partitions on each broker to achieve the first goal of evenly distributing

the partitions.

However, we have one other goal to achieve fault tolerance right. What is for tolerance?

It is as simple as making sure that if one broker fails for some reason, we still have a copy on some

other broker. Further, making sure that if the entire rack fails, we still have a copy on a different rack.

We can achieve fault tolerance by merely placing duplicate copies on different machines.

For example, the first partition has three copies.

All we need to do is to make sure that these three copies are not allocated to the same broker in the

above list.

How Kafka does that? Let us see this step by step process. Once we have the ordered list of available

brokers, assigning partitions is as simple as assign one to each broker using a round robin method. Kafka

starts with the leader partitions and finishes creating all leaders first.

So we take the leader of the partitions zero and assign it to broker 0.

The leader of partition one goes to broker 3, the leader of partition 2 learns on broker 1 and so on.

Once the leader partitions are created, it starts creating the first follower. The first follower allocation

simply starts from the second broker in the list and follows a round robin approach. So, we skip the first

broker in the list and place the first follower of partition 0 to broker 3, the first follower of partition

1 goes to broker 1. Similarly, the first follower of partition 2 goes to broker 4, and so on others are

also created.

Finally, it begins with the list of the second follower and maps them to the same broker list by jumping

one more broker from the previous start. So the second follower of partition zero goes to broker one,

and similarly, others are also created.

Great! We are done.

This is what happens when you created topic. Leaders and followers of the topic are created across the

cluster.

If you look at the outcome of this allocation, we couldn't achieve a perfectly even distribution.

The broker 0 has got four and broker 4 has got six partitions.

However, we made an ideal fault tolerance at the price of little disparity. Let's quickly check the fault

tolerance level.

The leader of the fourth partition is placed at the broker 2 that sits in the first track.

However, the first follower of partition 4 goes to a different track a broker.

5.

So even if one of the racks is entirely down, we still have at least one copy of the partition 4 available

on the other. Similarly the second follower is placed at a different broker B0.

So even if 2 brokers are down, we will still have at least one broker available to serve the partition 4.

You can validated with other partitions as well.

They all are well arranged in a way that at least two copies are placed on two different tracks.

This arrangement is ideal for fault tolerance.

Great.

So we learn how the replicas are distributed among the brokers in the cluster, making the system fault

tolerant. However, distributing the replicas among the brokers is the first half of the work distribution.

The second half of the problem is to define the responsibility of an individual broker. The session is

getting too long.

So, let's close it here, and we'll resume the discussion in the next lecture.

See you in the next lecture.

Keep learning and keep growing.