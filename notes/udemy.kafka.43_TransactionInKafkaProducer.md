---
id: sm8k7yuiwtmr83gy2gs2zhn
title: 43_TransactionInKafkaProducer
desc: ''
updated: 1699882864932
created: 1699882511229
---
Welcome Back.

In the earlier lecture,

we learned about the idempotent producer.

There is another advance producer concept.

We call it as

Transactional producer.

The transactional producer goes one step ahead of idempotent producer

and provides the transactional guarantee,

i.e., an ability to write to several partitions atomically.

The atomicity has the same meaning as in databases,

that means,

either all messages within the same transaction are committed,

or none of them are saved.

Let's create a simple example to understand the implementation details of a

transaction in Kafka Producer.

I am going to keep things very simple

and hence,

let's take the most simple example that we created earlier

and modify it to play a little

with transactions.

I am opening this hello producer example.

This one is not the starter project

but the completed hello producer project.

In this example,

we created one topic

and sent some messages to the same topic.

The first thing that I want to change in this example

is to copy the create-topic script and create another topic.

Great!

So now,

we have two topics.

hello-producer-1

and hello-producer-2.

We are going to implement a transaction

that would send some messages to both the topics.

When we commit the transaction,

the messages will be delivered to both the topics.

If we abort or rollback the transaction,

our messages should not be sent to any of these topics.

That's what atomicity means. Right?

Implementing transactions

requires some mandatory topic level configurations.

All topics which are included in a transaction

should be configured with the replication factor of at least three,

and the min.insync.replicas for these topics should be set to at least 2.

Let's look at our create-topic script.

We already have a replication factor of three.

So the first requirement is already met.

The second requirement is to ensure

that the min.insync.replicas is set to at least 2 for these topics.

Let's add that configuration to our create-topic command.

Great!

We are now all set from the topic perspective.

Now, these two topics can participate in a transaction.

Now,

I will go to AppConfigs class

Â and make some changes there.

Let me reduce the number of events.

I make it 2.

Why?

Just because it will be easy to verify the results with 2-3 records.

I have one constant for the topic.

But now we are going to deal with two topics.

So let me create one more constant for the other topic.

Great!

It looks good now.

Â Now I want to add one more constant here

named transaction ID.

I will use this value to set TRANSACTIONAL_ID_CONFIG for the producer.

Let me fix that configuration first,

and then I will explain it.

So,

here in my properties,

I am going to set a transaction id.

Setting a TRANSACTIONAL_ID for the producer is a mandatory requirement

to implement producer transaction,

and there are two critical points to remember here.

First-

When you set the transactional id,

idempotence is automatically enabled

because

transactions are dependent on idempotence.

Second and most important one -

TRANSACTIONAL_ID_CONFIG must be unique for each producer instance.

What does that mean?

That means you can't run two instances of a producer with same transactional id.

If you do so,

then one of those transactions will be aborted

because two instances of the same transaction are illegal.

The primary purpose of the transactional id is to rollback the

older unfinished transactions

for the same transactional id

in case of producer application bounces or restarts.

You might be wondering,

then how do I scale?

I mean,

how do I run multiple instances of the producer to achieve horizontal scalability.

Well, that is simple.

Each instance can set its own unique transaction id,

and all of those would be sending data to the

same topic implementing similar transaction

Â Â but all those transactions would be different

and will have their own transaction id. Right?

And that should be the case anyway.

Two customers

performing two transactions in parallel should have two unique transaction ids.

Isn't it?

That's all about transaction id.

Setting a transaction id is a mandatory requirement for the producer

to implement a transaction

and I always keep the transaction id outside the producer code

Â Â in a kafka.properties file

as I showed in one of the examples.

Great!

We are now ready to implement a transaction.

Implementing transaction in the producer is a three-step process.

The first step is to initialize the transaction

by calling initTransactions().

So,

let's do it.

This method performs the necessary check to ensures

that any other transaction initiated by

previous instances of the same producer is closed.

That means,

if an application instance dies,

the next instance can be guaranteed

Â that any unfinished transactions have been either completed or aborted,

leaving the new instance in a clean state before resuming the work.

It also retrieves an internal producer_id

that will be used in all future messages sent by the producer.

The producer_id is used by the broker to implement idempotence.

The next step is to wrap all your send() API calls

within a pair of beginTransaction()

and commitTransaction().

Let me do that as well.

Great!

In case you receive an exception that you can't recover from,

abort the transaction

and finally close the producer instance.

Simple. Isn't it?

All messages sent between the beginTransaction()

and commitTransaction() will be part of a single transaction.

I would also want to put some log entries,

so I know what is happening there.

We created two topics for this example.

My current code is sending messages to only one topic.

Let me duplicate this line

and make some changes,

so I send one message to the first topic

and another message to the second topic.

Nice!

So,

I am assuming that this transaction will begin here.

The loop will run twice.

I will send two messages in each loop,

one message to each topic.

We don't expect any exceptions,

and hence

the code is not likely to get into the catch block.

In normal condition,

we will commit the transaction here.

So far, so good.

Now, I want to add some code for the rollback scenario.

So let me duplicate this entire thing here.

This would be my second transaction.

My loop will again execute twice,

and I will send a total of 4 messages.

Two messages to each topic.

But this time,

instead of committing the transaction,

I will abort it.

So,

all the 4 messages

that I am sending in this second transaction will rollback.

If the begin,

commit and abort works correctly,

I would receive only four messages sent by the first transaction.

The messages sent by the second transactions should not appear in any of the topics. Right?

Let's test it.

Let me start the cluster services.

Great!

All services are up,

and we have the required topics.

Let's execute the application.

Good.

The first transaction committed,

Â Â and the second one is aborted.

Now its time to investigate the results.

I have added this Kafka console consumer script.

This script reads both topics.

So, If I execute the consumer.

I should see 4 messages tagged as T1.

I shouldn't see any message tagged as T2. Right?

Â Â Let's execute the consumer.

Do you see the expected results?

Great!

One final note about the transactions.

The same producer cannot have multiple open transactions.

You must commit or abort the transaction before you can begin a new one.

The commitTransaction() will flush any unsent records before committing the transaction.

Â Â If any of the send calls failed with an irrecoverable error,

that means,

even if a single message is not successfully delivered to Kafka,

Â the commitTransaction() call will throw the exception,

and you are supposed to abort the whole transaction.

And that's reasonable

because that is what a transaction means,

either all or nothing.

In a multithreaded producer implementation,

you will call the send() API from different threads.

However,

you must call the beginTransaction() before starting those threads

and either commit or abort when all the threads are complete.

Great!

That's all for this lecture.

See you again in the next lecture.

Keep Learning and Keep Growing.