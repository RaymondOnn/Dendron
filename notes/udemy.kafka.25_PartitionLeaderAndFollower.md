---
id: yb21lppb60ueqlu6jsxwm8b
title: 25_PartitionLeaderAndFollower
desc: ''
updated: 1699881607673
created: 1699881584567
---
Welcome back.

In the previous session, we learn how the replicas are distributed among the brokers in the cluster.

In this lecture, I'll talk about the responsibilities of the leader and the followers.

So, let's start.

We learned that the broker manages two types of partitions. Leader partition and Follower partition. Depending

upon the partition type,

a typical broker performs two kinds of activities. Leader activities and follower activities.

Let us try to understand it. In the example, we allocated 30 replicas amongst six brokers.

Now each broker owns multiple replicas.

For example, the broker 4 holds six replicas. Two of these replicas of the leader partitions, and the

remaining four are the following partition. So, the broker acts as a leader for the two leader partitions,

and it also acts as a follower for the remaining four follower partitions.

What does that mean?

Let's try to understand, what does it mean by a broker to act as a leader.

Regarding Kafka broker, being a leader means one thing. The leader is responsible for all the requests

from the producers and consumers.

For example, let's assume that a producer wants to send some messages to a Kafka topic.

So the producer will connect to one of the brokers in the cluster and query for the topic

metadata. All Kafka brokers can answer the metadata request, and hence the producer can connect to any

of the broker and query for that metadata.

The metadata contains a list of all the leader partitions and their respective host and port information.

Now the producer has a list of all leaders.

It is the producer that decides on which partition does it want to send the data, and accordingly send

the message to the respective broker.

That means the producer directly transmits the message to a leader. On receiving the message,

the leader broker persists the message in the leader partition and sends back an acknowledgement. Similarly,

when a consumer wants to read message.

It always reads from the leader of the partition.

We learn more detail about the producer and consumer interaction in the next section.

However, at this stage, you should be clear that the producer and the consumer always interact with the

leader. And that's what is the responsibility of the leader

broker. Interact with the producer and the consumer.

Now let's come back to the follower. Kafka broker also acts as a follower for the follower partitions

that are allocated to the broker. In the figure, the broker B4 owns four follower partitions and hence

the B4 acts as a follower for these replicas. Followers do not serve

producer and consumer requests. Their only job is to copy messages from the leader and stay up to date

with all the messages.

The aim of the follower is to get elected as a leader

when the current leader fails or dies. So, they have a single point agenda. Stay in sync with the leader.

Why?

Because they can't get elected as a leader if they are falling behind the leader and fail to be in sync

with the leader

by copying all the messages. The next question is this - How does the follower stay in sync with the leader?

To stay in sync with the leader, the follower connects to the leader and requests for the data. The leader

send some messages, and the followed persists them in the replica and requests for more.

This goes on forever as an infinite loop to ensure that the followers are in sync with the leader.

Great.

See you again.

Keep learning and keep growing.