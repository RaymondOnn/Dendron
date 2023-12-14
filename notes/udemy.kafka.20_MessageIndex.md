---
id: ho2jfpw2xq0dz4x6u4z21g3
title: 20_MessageIndex
desc: ''
updated: 1699881399476
created: 1699881151114
---

Welcome back.

If you want to locate a specific message, you must know at least three things.

Topic name, partition number, and then the offset number.

At first sight,

this arrangement of uniqueness looks strange.

Let me explain.

If you're thinking database terms, this unique identification does not make a good sense for locating

the messages.

The topic name is like a table name in the database. But partition and offset are just numbers. In database

applications,

we may want to access data on some critical columns such as invoice_number, customer_name, or customer_id.

However you cannot do that in Kafka because messages are not structured into column names. And that's

why this arrangement of uniqueness looks strange.

However this numbering may not be a problem for real-time stream processing application.

How?

Let's take an example to understand it. In a stream processing application,

the requirement is different.

A stream processing application wants to read all messages in a sequence.

Let's assume that you have a stream processing application that computes loyalty points for the customer

in real-time.

The application should read each invoice and calculate loyalty point.

While computing loyalty points, you need customer_id and the amount, but you must read all the events.

Let's look at the sequence of activities.

The application connects to the broker and asks for the messages is starting from the offset zero.

Let's assume the broker sense ten messages to the application.

The application takes a few milliseconds to compute loyalty points on those 10 invoices.

Now it is again ready to process a few more messages. So, it goes back to the broker and requests for

more messages starting from offset 0010.

The broker provides another batch of 15 messages.

Next time the application requests for another set of messages.

This process continues for the life of the application.

The process explained in this example is the typical pattern of how a stream processing application would

work.

In this process, you must have noticed that the consumer application is requesting messages based on

the offset.

The point is a straightforward. Kafka allows consumers to start fetching messages from a given offset

number.

This means, if consumer demands for messages beginning at offset hundred, the broker must be able to locate

the message for offset hundred. To help brokers rapidly find the message for a given offset,

Kafka maintains an index of offsets. The index files are also segmented for easy management, and they are

also is stored in the partition directory along with the log file segments.

You can see them here.

Finally, the last thing in this lecture. The time index. Kafka allows consumers to start fetching messages

based on the offset number.

However, in many use cases, you might want to seek messages based on timestamp.

These requirements are as straightforward as you want to read all the events that are created after

a specific timestamp. To support such needs,

Kafka also maintains the timestamp for each message builds a time index to quickly seek the first message

that arrived after the given timestamp.

The time index is like the offset index, and it is also segmented and stored in the partition directory

along with the offset index and log final segment.

You might see a few other types of files in the partition directory. But those files have nothing to

do with the data.

Kafka creates those files to keep some control information and clean them from time to time.

We do not care much about those files.

Great!

So it was a quite long session. But we learned some key concepts about the Kafka broker and how the Kafka

topics are logically organized.

See you in the next lecture. Keep learning and keep growing.