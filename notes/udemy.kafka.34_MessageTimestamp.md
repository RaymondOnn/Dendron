---
id: pbijhayxkyzq9tlt0553whd
title: 34_MessageTimestamp
desc: ''
updated: 1699882306707
created: 1699882007156
---
Welcome back.

We already learned about the producer partition.

The next thing is the timestamp.

The ProducerRecord takes an optional timestamp field.

The message timestamp is optional.

However, for a real-time streaming application, the timestamp is the most critical value. For that reason,

Every message in Kafka is automatically time stamped.

Even if you do not explicitly specify it.

Kafka allows you to implement one of the two types of message time a stamping mechanism. Create time

and Log Append Time. The CreateTime is the time when the message was produced.

The LogAppendTime is the time when the message was received at the Kafka broker. However, you cannot

use both.

Your application must decide between these two timestamping methods while creating the topic. Setting

up a default time a stamping method for a topic is straightforward.

You can set the message.timestamp.type topic configuration to 0 for using CreateTime,

or you can set it to 1 for using LogAppendTime. The default value is zero.

(CreateTime). The producer API automatically sets the current producer time to the ProducerRecord#timestamp

field.

However, you can override the auto time stamping by explicitly specifying this argument.

So, the message is transmitted with the producer time, either automatically set by the producer, or explicitly

set by the developer.

When using LogAppendTime configuration, the broker will override the producer timestamp with its current

local time before appending the message to the log.

In this case, the producer time is overwritten by the broker time.

However, the message will always have a timestamp, either a producer time or the broker time.

I prefer using default configuration as CreateTime whenever I'm using,

Producer API to bring data to Kafka because the producer API automatically assigns a timestamp.

However, when I'm using some other tool to bring data into Kafka, I need to understand how the tool handles

the time stamping.

A safer method is to configure the topic for LogAppend Time.

So even if your tool is not setting a time, at least the Broker will set some value.

Great.

See you again.

Keep learning and keep growing.

