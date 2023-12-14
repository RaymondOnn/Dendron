---
id: rll9wue2p5st0v9wvysdg4q
title: 30_CreateFirstProducer
desc: ''
updated: 1699882218689
created: 1699881930000
---
Welcome back.

Let's start creating some example to understand the mechanics of sending events to Apache Kafka.

To help you build the concept more clearly,

I want to take a problem solution approach. In this approach,

I describe a problem first.Then we'll go ahead and see how we can build and implement a solution for

the given problem.

Here is my first problem.

Problem-We want to create the simplest possible Kafka producer code that sends one million string messages

to a Kafka topic.

Simple isn't it.

How would you go about it?

So I have an empty starter MAVEN project here.

I named the project as Hello producer.

This starter and the empty project is also included in your course material.

All you need to do is to download the project and open it using IntelliJ idea.

Once loaded in IntelliJ,You should see the same thing that I am showing on the current screen.All

the necessary things are already set up in this project.

I have a pom.xml similar file with all the required dependencies.

I also have a log4j2.xml configuration file.Other than these basic things,

I have added an AppConfig class definition with some static constants.

We will be using these constants in the example, and I'll explain them in a minute.

I've also added some scripts to start your Kafka cluster on your local machine.

In most of the examples in this course,we will be using a three-node Kafka cluster setup on your local

computer.

Good! with this starter project template loaded,

We are now ready to write our first producer example.Let's create a class with a main method.I

name it as

Hello producer.

We define a logger so we can create some log entries.

This one is not mandatory but a good practice to have a logger.Next,Let's create a main method.

These are all simple things.

Isn't it.

Let me place a log entry.The log entry is to know if the program started.

Now we come to the real stuff.Sending data to Apache Kafka is a multi-step process.

The first step is to create a Java properties object and put some necessary configurations in it.

Kafka producer API is highly configurable,and we customize the behavior by setting different producer

configurations.

In this first example,we will set up four basic configurations.

These are the bare minimum configurations for the producer to work.The first configuration is the

CLIENT_ID_CONFIG, which is a simple string that is passed to the Kafka server.

The purpose of the client id is to track the source of the message.

The second one is BOOTSTRAP_SERVER_CONFIG.

The BOOTSTRAP_SERVER_CONFIG is a comma-separated list of host/port pairs.The producer will use this

information for establishing the initial connection to the Kafka cluster.

If you are running on a single node Kafka, you can supply an individual host/port information.The bootstrap

configuration is used only for the initial connection.

Once connected,the Kafka producer will automatically query for the metadata and discover the full list

of Kafka brokers in the cluster.

That means you do not need to supply a complete list of Kafka brokers as a bootstrap configuration.However,

it is recommended to provide two to three broker addresses of a multimode cluster.

Doing so will help the producer to check for the second or third broker in case the first brokers in

the list is down.

I have defined all these configuration values as constants in the appConfig

Class,and that is why I added this class in the starter project template.

Remaining two mandatory configurations are the key and the value serializers.

great.

Now there are two concepts here.

The first one is the key/value pair.A Kafka message must have a key/value structure.

That means each message that we want to send to the Kafka server should have a key and a value.

You can have a null key, but the message is still structured as a key/value pair.

The second concept is about serializer.Kafka

messages are sent over the network.So,the key and the value must be serialized into bytes before they

are streamed over the network.

Kafka producer API comes with a bunch of ready to use serializer classes.

In this example, we are setting an IntegerSerializer for the key and a StringSerializer for the

message value.

That's all about the first step.Step two is to create an instance of KafkaProducer.

The key for my message is going to be an integer, and the value would be a string.

We need to pass the properties that we created earlier to the constructor.That's all.The third step is

to start sending messages to Kafka.

So what I want to do is to create a loop that executes a million times.Inside the loop,

you can send the message.

The send method takes a ProducerRecord object and sends it to the Kafka cluster.

So, we create a new producer record.The ProducerRecord constructor takes three arguments.

The first argument is the topic name.We send messages to Kafka topic.Right?

So,this is the topic name.The second argument is the message key.Let's pass the loop counter as a key.

The final argument is the message itself.

We want to send a simple string message. To keep it unique,

Let me concatenate the loop counter.Great! So your third step is over.

Once this loop completes,you would have sent a million string messages to the Kafka cluster.Right?

The last and final step is to close the producer instance. The producer functionality is involved,

and it does a lot of things internally.We will cover producer internals as we progress with this course.

However,it is essential to understand that the producer consists of some buffer space and background

I/O thread.

If you do not close the producer after sending all the required messages, you will leak the resources

created by the producer.

That's all.

Let me quickly summarize.

Sending messages to Kafka using producer API is a four-step process.In the first step you will set

some configurations that will control the behavior of your producer API.

The second step is to create a producer object.

The third step is to send all the messages.

Finally,if you have no further messages to send,close the producer.The final completed project file

is also included in your course material.

But I recommend that you pick up the starter project,and try creating the completed example yourself.

If you have some doubts,you can refer to the completed project files that I have included in your course

material.

Great!

Do you want to execute this example?

Let's do it.

Start your zookeeper

Then start all three Kafka brokers.

Wait for the brokers to start.Finally,create the topic.

All these scripts are already included in your starter project.

Great!

You are now ready to execute the example.Start sending a message and finished sending the message.

It took a few seconds to send a million small messages.

Great!

Isn't it?

That's all for this video.

In the next video,we will learn some internal of Kafka producer.See you in the next lecture.

Keep learning and keep growing.

