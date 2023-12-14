---
id: 0p9xl6ldhtgds9ecffxo85h
title: 01_WhatIsKafka
desc: ''
updated: 1701105759106
created: 1699880203329
---



### What is Apache Kafka? 
- Apache Kafka is a distributed streaming platform. 
  - Can create more than one real-time streams of data.
  - Real-time processing of streams to produce results
  - 
 
And bringing data from your smart meter to your Kafka server is what we call creating real time streams. 
Now once your data is coming and landing into Apache Kafka server, you have also created an application to read and process this data. 
Your application might be doing a variety of things. 
For example, you are computing and monitoring the overall electricity load for every house. 
As soon as the charge goes above a predefined threshold, ( let's say 2KVA), you are sending an SMS to the house owner to alert him. 
But this kind of SMS alert doesn't make any sense after an hour, and you want to send such alerts in seconds or at least in 2-3 minutes. 
Right? So your application is not accumulating data and then processing it to compute, if the threshold is broken. 
It must be continuously listening to the data and processing it as soon as the data arrives at the Kafka's server. 
And that is what we call real-time stream processing. 
So the answer to the first question is this - Apache Kafka is a highly scalable and distributed platform for creating and processing extremes in real time. 
Now the next question. 
How does it work? Kafka adopted Pub/Sub messaging system architecture, and it works as an enterprise messaging system. 
I hope you already understand enterprise messaging system architecture. 
If not, nothing to worry about. 
I'm going to explain it right here. 
A typical messaging system has got three components. 
Producer, Message broker And Consumer. 
The producer is a client application that sends data records. 
These data records are called messages. 
The broker is responsible for receiving messages from the producers and storing them into local storage. 
Finally, the consumers are again client applications that read messages from the broker and process them. 
In this architecture, the broker is in the center and acts as a middleman between producers and consumers. 
If you recall the same smart meter example, the left side of the system including your smart meter, acts as a data producer. 
The Kafka server in the center is the message broker. 
Finally the right side box where you have an application is the consumer. 
This is a fifty thousand feet view of how Kafka works, but it answers your question - How Kafka works? Kafka works as a Pub/Sub messaging system, where we create producer applications to send data as a stream. 
We install and configure Kafka server to act as a message broker. 
And finally, we create consumer applications to process the data stream in real-time. 
That is all. 
The next question is this - Where does it come from? Kafka was initially conceptualized and developed by LinkedIn and later open sourced in 2011. 
However, in the beginning Kafka was designed to handle the data integration problem. 
Let us try to understand the LinkedIn problem. 
Here is a diagram that I borrowed from Jay Kreps's blog. 
Jay Kreps was leading Kafka at LinkedIn, and now he is the CEO of Confluent Inc. 
So this diagram explains the data integration problem in a large enterprise. 
In this diagram, all these boxes are different. 
application backends. 
They all generate and store some data. 
However, the data generated that one application is often needed by other applications as well. 
So you have a requirement to share or send data from one application to another application. 
All these lines are showing data movement from a source application to a destination application. 
In a growing organization, the number of source and destination system keeps growing. 
Finally, you will end up in a situation like this. 
Some parts of these pipelines will keep breaking every day, and situation becomes like a big mess to maintain. 
LinkedIn tried to solve this problem using Pub/Sub messaging architecture, and the result looked like this. 
They placed a broker in the middle, and other applications started working as a producer and consumer. 
This architecture offered them a bunch of advantages and simplified this solution. 
We will learn more about the benefits of this architecture. 
However, you got your answer to the question. 
Where does it come from? The next question is this - How it evolved from a data integration solution to a streaming platform. 
Kafka initially started with two things. 
Server software that you can install and configure to work as a message broker. 
A Java-based client API library to help with the following. 
Create Kafka producer applications and create Kafka consumer applications. 
But later, Kafka aspired to become a full fledged real-time streaming platform. 
And to achieve that objective, they augmented Kafka with three more components. 
Kafka Connect, Kafka Streams and KSQL. 
Kafka connect and Kafka Streams are still open source components, and they are available with Apache 2.0 license. 
However KSQL is available with some licensing restrictions and offered by Confluent Inc as a commercial tool. 
So to answer your question from 2011 to 2019 Kafka evolved as a set of five components. 
Kafka broker - Which is the central server system Kafka Client API - Which is Producer and Consumer APIs. 
Kafka Connect which addresses the initial data integration problem for which Kafka was initially designed. 
Kafka streams This one is another library for creating real time extreme processing applications. 
KSQL with KSQL Kafka is now aiming to become a real time database and capture some market sharing Databases and DW/BI space. 
Finally the last question of this lecture. 
Where does it fit into an enterprise application ecosystem? By adopting Pub/Sub semantics, Apache Kafka is becoming the circulatory system of your data ecosystem. 
In this case, just like your circulatory system carries blood. 
Kafka brings data to various members of the infrastructure. 
Kafka occupies a central place in your real time data integration infrastructure. 
The data producers can send data like messages, and they can send it to Kafka brokers as quickly as the business event occurs. 
Data consumers can consume the messages from the broker as soon as the data arrives at the broker. 
With careful design, the messages can reach from producers to consumers in milliseconds. 
The producers and consumers are completely decoupled, and they do not need tight coupling or direct connections. 
They always interact with the Kafka broker using a consistent interface. 
Producers do not need to be concerned about who is using the data, and they just send the data once without worrying about how many consumers would be reading it. 
Producers and consumers can be added, removed, and modified as the business case evolves. 
Great! That's all for the overview. 
See you in the next lecture. 
Keep learning and keep growing. 
