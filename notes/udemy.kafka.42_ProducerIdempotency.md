---
id: tbx6amqikpj6ozz0x88tn9v
title: 42_ProducerIdempotency
desc: ''
updated: 1699882847379
created: 1699882481107
---
Welcome Back.

We already learned that Kafka is at-least once system by default.

And you can configure it to get at-most once.

However,

some use cases want to implement exactly-once semantics.

I mean,

we don't lose anything,

and at the same time,

we don't create duplicate records.

To meet exactly once requirement,

Kafka offers an idempotent producer configuration.

All you need to do is to enable idempotence,

and Kafka takes care of implementing exactly-once.

To enable idempotence,

you should set the enable.idempotence producer configuration to true.

Â Once you configure the idempotence,

the behavior of the producer API is changed.

There are many things that happen internally,

but at a high level,

the producer API will do two things.

It will perform an initial handshake with the leader broker

and ask for a unique producer id.

At the broker side,

the broker dynamically assigns a unique ID to each producer.

The next thing that happens is the message sequencing.

The producer API will start assigning a sequence number to each message.

This sequence number starts from zero

and monotonically increments per partition.

Now,

when the I/O thread sends a message to a leader,

the message is uniquely identified by the producer id and a sequence number.

Now, the broker knows that the last committed message sequence number is X,

and the next expected message sequence number is X+1.

This allows the broker to identify duplicates as well as missing sequence numbers.

So,

setting enable.idempotence to true will help you ensure

that the messages are neither lost not duplicated.

How exactly it happens is not much relevant.

We leave that on producer API and broker.

All you need to do is to set the configuration to activate this behavior.

However,

you must always remember one thing.

If you are sending duplicate messages at your application level,

this configuration cannot protect you from duplicates.

That should be considered as a bug in your application.

Even if two different threads

or two producer instances are sending duplicates,

that too is an application design problem.

The idempotence is only guaranteed for the producer retires.

And you should not try to resend the messages at the application level.

Idempotence is not guaranteed for the application level message resends.

Or duplicates

send by the application itself.

Great!

That's all for this lecture.

Keep Learning and Keep Growing.