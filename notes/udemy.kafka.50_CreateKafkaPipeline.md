---
id: 1oqzn79pm1pugwawenv9hbs
title: 50_CreateKafkaPipeline
desc: ''
updated: 1699883203077
created: 1699882678627
---
We already learned a lot about producer APIs.

In this lecture,

I will give you a practical introduction to Kafka Consumer APIs.

Same as earlier,

let's take a problem-solution approach to learn Kafka Consumers.

So,

here is my problem statement.

In the earlier lecture,

we created a POS simulator application that generates a series of invoices

and sends them to a Kafka Topic.

Now, in this example, we want to implement a miniature form of a real-time data validation service for invoices.

So,

what we want to do is to read all invoices in real-time.

Apply some business rules to validate the invoice.

If the validation passed,

send them to a Kafka topic of valid invoices.

If the validation failed,

send them to a Kafka topic of invalid invoices.

As a result,

we will be able to segregate valid and invalid invoices into two different topics.

We will end the example there.

However,

the overall system might look something like this.

In this figure,

all valid records are consumed by a data reconciliation application

where the invoices are worked upon to rectify the issues

and sent back to the pool of valid records.

All the correct records are consumed by other applications and microservices to achieve some business objectives.

Make sense?

Great!

Letâs build a real-time data validation service.

The first thing that we need is a business rule to define an invalid invoice.

What is an Invalid Invoice?

For our purpose,

letâs define a simple rule.

An invoice is considered invalid if it is marked for home delivery,

but a contact number for the delivery address is missing.

Simple.

Isnât it?

Same like earlier,

I have got you a starter project

that comes with necessary dependencies,

scripts,

a bunch of predefined classes to model invoices,

a JSON serializer

as well as a deserializer

and an AppConfig class.

You are already familiar with all of these things

because I explained them in earlier examples.

So, the stage is set.

Letâs create a class

with the main() method.

Now we are ready to create a Kafka consumer.

Creating a Kafka consumer is a four-step process,

and it is very similar to creating a Kafka producer.

The first step is to create a Java Properties

and set required Kafka consumer configurations.

Same as producer API,

Kafka consumer API is also highly configurable,

and we customize the behavior by setting the config parameters.

The first setting is the client id.

You already know it. Right?

The second one is the bootstrap server.

You know this one also. Right?

Other two configurations are the key and the value deserializer.

The Kafka producer transmits a message after serializing them to raw bytes.

Right?

Now at the consumer,

we must deserialize the record to an appropriate object.

Since the example is intending to read the messages sent by the POS simulator,

and the POS simulator used JSON serializer.

Hence,

the corresponding consumer must be using a JSON deserializer.

The next configuration, VALUE_CLASS_NAME_CONFIG,

is the target deserialized Java Class name.

We want our message to be deserialized to a POSInvoice.

Right?

Great!

Let me add a couple of more configurations.

These configs are specific to the following concepts.

Kafka Consumer Groups

and Kafka offsets and consumer positions

I will talk about these concepts in the next lecture.

But for now,

letâ s move on to step 2 of creating a Kafka consumer.

Step two is straight.

Create an instance of KafkaConsumer class.

My key is coming as String,

and the value is coming as

PosInvoice.

We need to supply the configurations

that we created in step one.

Thatâs it.

Step 3 is to subscribe to the topics that we want to read.

A Kafka consumer can subscribe to a list of topics.

However,

in this example,

we want to read a single topic.

Finally,

the last step.

Read the messages in a loop.

Mostly, this loop would be an infinite loop.

Why?

Because we are expected to keep reading the records

Â and process them in real-time for the life of the application.

This thing should never stop.

So, we need an infinite loop.

Great!

The next question is

How do we read?

Let me write the code and then I will explain.

After subscribing to the topics,

the consumer can start requesting the message records

by making a call to the poll() method.

The poll() method will immediately return an Iterable ConsumerRecords.

If there are no records at the broker,

it will wait for the timeout.

When the timeout expires,

an empty ConsumerRecords will be returned.

Now,

we will loop through all the records that we received.

Great!

So we have this nice loop

where we can process each record

and do whatever we want to do.

Right?

We wanted to perform some data validation.

Letâs do it.

If the record value delivery type is equal to the home delivery

and the record value delivery address contact number is empty,

then it is an invalid record.

Else,

it is a valid record, right?

So far, so good.

But we wanted to send these invalid

and valid records to separate Kafka topics.

And that would require creating a producer.

So,

letâs create a producer.

You already learned that in the earlier lectures. Right?

Create a properties object.

Â Set some mandatory configurations.

Then, create a producer.

Thatâs all.

Now, we can use this producer to send the invoices.

Producer dot send.

Then create a new Producer Record.

You already learned all this in earlier lessons.

The first argument is the topic name.

Then the message key.

Ok, we used to set null keys in previous examples.

But now, let me set the store id as the key.

Then the record value itself.

I will copy the same thing to the else part and change the topic name.

Thatâs all.

We are done.

Let me quickly summarize whatever we have done here. Â 

We defined consumer configurations,

and then we created a Kafka consumer using those configs.

In the next step,

we subscribed to a Kafka topic and finally started polling the brokers.

Each poll call will provide a set of invoices,

we process them and again poll for next set of invoices.

This example is the most basic form of a stream processing application.

However, it implements a consume-transform-produce pipeline.

In this pipeline,

we consume invoices,

identify if they are valid or invalid,

and produce the valid invoices to a Kafka topic

Â and invalid ones to a different topic.

You can test this application easily.

Letâs do it quickly.

Â Let me load the POS simulator example in a different IDE.

I will start Kafka cluster services.

Create POS topic.

Execute POS simulator.

And finally,

come back to my POS validator project.

Invoices are ready.

Â Â Â Invoices are already being sent to my local Kafka Cluster,

and they are waiting there for getting processed.

Let me create valid and invalid topics.

These are my end result topics. Right?

Now, we can start POS validator.

You can see these log entries.

Processing got started.

Now,

I can start a console consumer to check my invalid invoice topic.

Great!

I have got some invalid invoices here.

While the example is working perfectly fine,

and at first sight,

we do not see any issues in this example.

However,

there is a lot that goes inside the Kafka consumer

and creating a real-time stream processing application

requires a lot many other considerations,

including scalability and fault tolerance.

In the next lecture,

I will discuss the consumer scalability,

fault tolerance,

See you in the next lecture.

Keep Learning and Keep Growing.