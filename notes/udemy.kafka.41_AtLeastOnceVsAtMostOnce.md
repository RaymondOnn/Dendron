---
id: 5istq4ytncng2ajmjp8ataa
title: 41_AtLeastOnceVsAtMostOnce
desc: ''
updated: 1699882830078
created: 1699882455151
---
Welcome back.

In the earlier lectures,

you learned to create Kafka producers,

and I am assuming that you are now comfortable in meeting most of the basic requirements

of streaming events to the Kafka cluster.

However,

some specific and intricate scenarios require some extra attention.

In this lecture,

I will talk about some advanced producer concepts.

Let's start.

Apache Kafka provides message durability guaranties

by committing the message at the partition log.

The durability simply means,

Once the data is persisted by the leader broker in the leader partition,

we can't lose that message till the leader is alive.

However,

Â if the leader broker goes down,

we may lose the data.

To protect the loss of records due to leader failure,

Kafka implements replication, right?

Kafka implements replication using followers.

The followers will copy messages from the leader

and provide fault tolerance in case of leader failure.

In other words,

when the data is persisted to the leader

as well as the followers in the ISR list,

we consider the message to be fully committed.

Once the message is fully committed,

we can't lose the record until the leader,

and all the replicas are lost,

which is an unlikely case.

However,

in all this,

we still have a possibility of committing duplicate messages

Â due to the producer retry mechanism.

As we learned in the earlier section,

if the producer I/O thread fails to get a success acknowledgment from the broker,

it will retry to send the same message.

Now,

assume that the I/O thread transmits a record to the broker.

The broker receives the data and stores it into the partition log.

The broker then sends an acknowledgment for the success,

and the response does not reach back to the I/O thread due to a network error.

In that case,

the producer I/O thread will wait for the acknowledgment

and ultimately send the record again assuming a failure.

The broker again receives the data,

but it doesn't have a mechanism

to identify that the message is a duplicate of an earlier message.

Hence,

the broker saves the duplicate record causing a duplication problem.

This implementation is known as at-least-once semantics,

where we cannot lose messages

because we are retrying until we get a success acknowledgment.

However,

we may have duplicates

because we do not have a method to identify a duplicate message.

For that reason,

Kafka is said to provide at-least-once semantics.

Kafka also allows you to implement at-most-once semantics.

How?

Well, that`s easy.

You can achieve at-most-once by configuring the retires to zero.

In that case,

you may lose some records,

but you will never have a duplicate record committed to Kafka logs.

Great! See you again.

Keep Learning and Keep Growing.