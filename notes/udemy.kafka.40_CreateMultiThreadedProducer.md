---
id: o7d4b35pe4do23ylgxu6ueb
title: 40_CreateMultiThreadedProducer
desc: ''
updated: 1699882811679
created: 1699882427876
---
Let's create a scenario that we can use to create an example

and understand the producer thread implantation.

Here is the problem statement.

Assume you have multiple data files

and you want to send data from all those files to the Kafka Cluster.

So,

basically what we want to do is

to create one main thread that reads a bunch of data files

and create one independent thread to process each data file.

For example,

if you are supplying three data files to the application,

it must create three threads,

one for each data file.

Each thread is responsible for reading records from one file

and sending them to Kafka cluster in parallel.

In all this,

we do not want to create a bunch of Kafka producer instances,

but as a recommended best practice,

we want to share the same Kafka Producer Instance among all threads.

Great!

The problem is defined.

How you wanna go about the solution?

Same like earlier,

I have created a starter project for you,

and it is included in your course material.

If you load the starter project file in your IntelliJ idea,

you will see something similar to my current screen.

Like the earlier example,

the starter project comes with maven pom file to define the dependencies,

preconfigured log4j2.xml,

scripts to start your Kafka cluster,

create Kafka topic,

and start a console consumer if you want to look at the received messages.

I have also included two sample data files.

We will be writing a multithreaded producer to send data from these files to the Kafka cluster.

Same like earlier,

the project also includes an AppConfig class

where I have defined some constants

that we will be using in the code that we are going to write.

This time,

I have also included a Kafka.properties file

to help you understand

how can you keep some producer level configurations outside the source code.

Basically,

we want to keep the Kafka Broker coordinates outside the application code.

This arrangement allows us to deploy the application

and connect to any Kafka cluster

by simply changing the connection details in the properties file

which resides outside the application.

Great!

That's all I preconfigured in your starter project.

Rest of the things are going to be a live coding session.

Let's start.

To solve this problem,

we need at least two classes.

The first one is required to create a producer thread.

I am going to implement Java Runnable Interface

to define a class named as Dispatcher.

The Dispatcher class implements the Runnable interface.

The Runnable interface allows us to execute an instance of this class as a separate Thread.

The second part of the puzzle is to implement a main() method to create threads

and start them.

I am going to implement a separate class

named DispatcherDemo,

which would have a main() method.

Now with two classes defined,

let's work on the Dispatcher class first.

The Dispatcher class will do the following things.

Read all records from a given data file.

So let's create a private member for the data file.

Send all the records from the file to a given Kafka topic.

So, let's create a private member for the Kafka topic.

In order to send the data to Kafka,

we need a Kafka Producer.

So let's create a private member for the producer as well.

All these things will be given to the Dispatcher by the main application thread.

So, let's create a constructor to take these values.

Great!

Finally, we need to override the run method

and implement all the steps that I talked about.

Let's do it.

The first thing is to get the data file handle.

Then create a file scanner.

Ok,

Let me add a try-catch here.

Start scanning each line of the file.

Good.

Now I have a nice loop where I get each line of data from the file.

We need to send these lines to Kafka.

So, let's do producer.send.

You already learned that the send method takes a producer record.

We provide the topic name,

Â Â a message key,

which could be null,

and finally,

the message value.

That's all.

You can adjust your try catch block the way you want.

You may also want to add a counter to count the number of lines.

Also,

put up a log message for starting.

Similarly,

one more for finishing.

Great!

My runnable class is ready.

Now, let's move to the DispatcherDemo and complete that one.

The first thing is to create a Kafka Producer.

And I hope you already know the steps.

We define a properties object.

Now, I want to do two things.

The first thing is to load some properties

that I defined in the Kafka.properties file.

You can do that by creating an input stream from a file.

Then load it into the properties.

That's it.

Finally,the second part.

Put other necessary configurations directly into the properties.

Great.

We are now ready to create a Kafka producer.

Let's do it.

The key would be an integer,

Â I am giving null as the key in the Dispatcher,

but that should be fine.

The value is a string.

Create an instance of the producer supplying the properties.

Now we are ready to create dispatcher threads.

Let me create an array.

Each element of this array

will hold the handle for the thread

so I can join them to wait for their completion.

The number of threads must be equal to the number of files.

In our case,

the length will result as two.

Good.

Now, I will create a loop.

Inside the loop,

create a new thread.

We will use the dispatcher class constructor for creating the thread.

We will be passing the same producer instance.

So all the threads will use the same producer,

and that's what we wanted to do. Right?

Rest of the parameters are straightforward.

Topic name

and the file location.

Simple!

Isn't it?

Let's start the thread.

In the end,

in a separate try-catch block,

loop through the thread handles,

and join them together.

This joining will allow the main thread to wait for all the threads to complete.

Â Finally, don't forget to close the producer.

Great!

We are done.

Let me quickly summarize it,

and then we will run this test application.

We created a runnable dispatcher that takes a Kafka producer instance,

topic name,

and the file location.

Then we created a main method which would create a single instance of Kafka producer.

It will then create two threads using the runnable Dispatcher and pass the producer instance,

along with topic name

and file location to the dispatcher thread.

Each dispatcher thread will simply send all the lines from the file to the given Kafka topic.

The main thread will wait for all the threads to complete

and finally close the producer and terminate.

That's all.

If you want to run this application.

Start the zookeeper,

Start all Kafka brokers.

Create a topic.

And execute the producer application.

Great!

All the lines from both the files are sent.

Re-execute the producer,

and it will send all the data once again.

You want to start clean.

Â Stop your Kafka Brokers,

Zookeeper,

and delete this tmp directory.

And restart your zookeeper and Kafka brokers again and repeat the test.

The tmp directory is the data directory for zookeeper and Kafka brokers.

When you delete it and restart your services,

Â Kafka server starts fresh.

Good.

That's all for this session.

See you in the next lecture.

Keep learning and keep growing.