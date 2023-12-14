---
id: n729ohdgbdwn0sky71elx4s
title: 53_ChallengesWithConsumers
desc: ''
updated: 1699883298818
created: 1699883279899
---
Let's look at the scenario that we implemented in our earlier example.

We have a consumer.

Right?

We poll() some messages.

Segregate them into valid and invalid.

Send them to different topics.

And before we poll() again for more messages,

the consumer crashed.

What will happen?

The committed offset at the broker is still null.

Why?

Because the committed-offset is updated when we poll again.

That is the time when the broker assumes

that we successfully processed the earlier ones

so those messages should be committed to avoid a resend.

But in our case,

we already processed

Â Â Â but crashed

before we could poll again to let the broker know about the successful processing.

This situation is going to create duplicates for sure.

How do you handle this gotcha?

We learned about implementing Kafka transactions.

Right?

You can execute transactions to handle this scenario.

However,

that requires extra custom coding,

testing

and still leaves more space for introducing new bugs.

Similarly,

think about another scenario.

Instead of merely performing validation,

Â Â Â you wanted to compute total sales by store id.

How would you do it?

At first sight,

it looks simple.

Read the store id

and total sale value from the invoice.

Insert it to a key/value map.

Where the key is the store id,

and the value is the total sale.

For the next invoice,

do the same but this time sum it to the previous amount. Â 

Simple?

Isn't it?

But what about fault tolerance?

What if your application crashed?

You would lose the in-memory map.

Correct?

So you need to save it somewhere.

Maybe on your local disk.

But what if the crash was due to disk failure?

Ok,

so you can maintain that map in a remote database.

That looks safe. Right?

But you have to write a lot of custom code again,

and extend your transaction up to the remote database to avoid duplicate processing. Â 

Right?

You also need to handle database concurrency,

performance,

and additional network latency.

Now think about computing store wise total sale in a five-minute window.

I mean,

adding one more dimension of the time to whatever you are computing.

Similarly,

think about joining two topics in real-time.

I mean, one consumer is reading topic A,

another consumer is reading topic B,

and you want to join these two topics

and perform some computation.

All that can be done,

but not that easy.

You will end up writing a lot of complex code,

increasing the cost

and complexity of your application

The point is straightforward.

Basic features

and facilities for creating real-time stream processing applications

are missing from the Kafka Consumer APIs.

That is where Kafka Streams API turns out to be handy

and helps you to model most of the stream processing requirements easily.

Great!

Keep Learning and Keep Growing.