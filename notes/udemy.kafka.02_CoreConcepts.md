---
id: dbwnj7qbuqpjxz61jln7m2t
title: 02_CoreConcepts
desc: ''
updated: 1700013633836
created: 1699880250816
---
Welcome Back to Kafka Big Picture for Beginners. 
In the earlier lecture, I talked about five core components of Kafka ecosystem. 
In this lecture, I'll be explaining some fundamental concepts that are associated with Kafka. 
These are the core concept. 
And the rest of the training is built on your clear understanding of these terminologies and their meaning. 
So let's start. 
The first item is the producer. 
So what is a producer? A producer is an application that sends data. 
Some people call it data, but we often call them a message or a message record. 
Ultimately, it is a small to medium sized piece of data. 
The message record may have a different meaning and schema or record structure for us. 
But for Kafka, it is a simple array of bytes. 
For example, if I want to send a data file to Kafka, I'll create a producer application, and send each line of the file as a message. 
In this case, a message is one line of text, but for Kafka it is just an array of bytes. 
Similarly, if I want to send all the records from a database table, I'll post to each row as a message. 
Similarly if you want to send the result of a database query, you are going to create a producer application. 
The producer is going to fire a query against your database, collect the result and start sending each row as a message. 
So while working with Kafka if you want to send some data, you may have to create a producer application. 
You might find an out of the box ready to use producer which fits your purpose. 
But we'll talk about that situation later. 
The next item is the consumer. 
What is a consumer? The consumer is an application that receives data. 
If the producers are sending data they must be sending it to someone. 
Right? The consumers are the recipients. 
But remember, the producers are not going to send data directly to the recipients. 
They just send it to the Kafka server. 
And anyone interested in that data should come forward and consume the data from the server. 
So an application that is going to request data from the Kafka server is up consumer. 
And they can ask for the data sent by any producer provided that they have permissions to read it. 
So if we come back to the data file example, when I want to read the data file sent by a producer I'll create a consumer application. 
Then I'll request the Kafka server for the data. 
The Kafka server will send me some messages. 
I guess you remember that each message record is a line of text from the data file. 
So, the consumer application will receive some lines from the Kafka server. 
It will process them and again request for some more messages. 
This goes in a loop. 
The consumer keeps asking for the messages, the server keeps giving new records, and this goes in a circle as long as new messages are arriving at the kafka server. 
Now it's up to the consumer application on using the data. 
They might want to compute some aggregates and send some alerts. 
That part is up to the consumer application. 
The next item in our list is the broker. 
What is a broker? The broker is the Kafka server. 
It is just a meaningful name that was given to the Kafka server, and this name makes sense as well. 
Why? Because all that the server is doing is to act as a message broker between producer and consumer. 
I mean the producer and consumers do not interact directly. 
They use the Kafka server as an agent or a broker to exchange messages. 
Right? Great! What is next? What is a cluster? If you have any background in distributed systems, you already know that a cluster is a group of computers acting together for a common purpose. 
Kafka is a distributed system, and hence the cluster means the same thing for Kafka. 
So the Kafka cluster is a group of computers, each running one instance of the Kafka broker. 
That's all. 
Great! The next item is the topic. 
What is a Topic? We already learned that the producer will be sending data to the broker. 
On the other side, the consumer will be asking data from the broker. 
But the question is, what data or which data? The broker might say, Guys, I am collecting data from multiple producers. 
Which one do you want? OK. 
Give me the data sent by the smart meter producer. 
Well the smart meter producer is sending three different types of data. 
current-load - sent every minute. 
consumed-units This one is sent every hour. 
input-current-fluctuations This one is sent as and when it happens. 
Which one do you want? Well, give me the current load messages. 
OK so you are looking for a current load message. 
That's great. 
But we have a bunch of smart meter producers, and all of them are sending the current load messages. 
So which one of those are you interested in. 
Gosh! That's too much confusion. 
We must have some standard methods to cut down these back and forth questioning, right? And that's where we have the notion of Topic. 
So the topic is an arbitrary name given to a data set. 
You better say - it's a unique name for a data stream. 
If you're coming from a database background, you can also think of it as a database table. 
Creating a topic is a design time decision. 
So when you are designing your application, you as an architect are responsible for creating one or more topics. 
Once your topic is there, the producers and the consumers are going to send and receive data by the topic. 
Now let's come back to our smart meter example. 
Let's assume I created three topics. 
Current-Load-Topic, Consumed-Units-Topic, Input-Fluctuations -Topic. 
When my smart meter producers are sending data, they'll send all the current load messages to the current load topic. 
Similarly they'll be sending consumed the units and input fluctuations to their own topics. 
Now you look at the consumer to broker communication. 
The consumer application will request like this. 
Give me all the data that you received in the current load topic. 
Simple isn't it? The broker knows the topic, and it won't have any further questions. 
In the database world, you can think of the Kafka topic as a table. 
Producers can insert new records in the table and hence to the topic. 
The consumers can read those records from this same table, and hence from the same topic. 
Right? But remember, all the communication happens via a broker. 
There is no direct interaction between producer and consumer. 
Great! The next item in our list is the partitions. 
So what are topic partition? By now, you learn that the broker would store data in the topic, and the topics are just like a database table. 
This data could be massive. 
It may be larger than the storage capacity of a single computer. 
Think of the hundreds of thousands of smart meters sending current load every minute. 
In just a few hours, you are going to collect several terabytes of data. 
Isn't it? So the broker is going to face a storage capacity challenge. 
One obvious solution is to break the topic into multiple smaller parts and distribute it over multiple computers in the Kafka cluster. 
We already learn that Kafka is a distributed system, and it runs on a cluster of computers. 
So it is evident that Kafka can easily break the topic into smaller partitions and store those partitions on different machines. 
This approach will solve the storage capacity problem, and that's where the topic partition means. 
A small and independent portion of the topic, right? However you might have another question. 
How will Kafka decide the number of partitions for a topic? I mean, some partitions might be reasonably large, and others may be comparatively small. 
For example the current load topic is receiving data every minute and consume units units are collecting data every hour. 
So the current load topic is going to be huge whereas the consumed units will be a smaller topic. 
So the question is this how Kafka would know if the current load topic might need hundred partitions and the consumed units topic might need just 20 partitions. 
Well the answer is simple. 
Kafka doesn't make that decision. 
The number of partitions in our topic is a design decision. 
So we as an architect are going to decide the number of partitions for each topic. 
When we create a topic we need to specify the number of partitions that we need, and the Kafka broker will produce it. 
That's all. 
But remember, the partition is the smallest unit and it is going to be sitting on a single machine. 
You cannot break it again, so you must be doing some meaningful estimation to decide the number of partitions for each Topic. 
Great! The next item is the Offset. 
What is partition Offset? Well, the offset is a simple thing. 
It is a unique sequence ID of a message in the partition. 
The sequence ID is automatically assigned by the broker to every message record as it arrives in the partition. 
Once assigned, These ideas are not going to change. 
They are immutable. 
So when the first messages are stored in a partition, it gets an offset ID as zero. 
The next one gets one then two and so on. 
This sequencing means that the Kafka stores messages in the partition, in the order of arrival. 
And the offset ID is clearly an arrival order number. 
But remember these offsets are local within the partitions. 
There is no global ordering in the topic across partitions. 
So if you have three partitions in a topic within each partition, the offset is going to start from zero and increase by 1. 
So if you want to locate a specific message, you must not read things. 
Topic name, Partition number and then the offset number. 
If you have these three things you can directly look at a message in the Kafka cluster. 
Great. 
We are almost done except the last thing. 
What is a consumer group? We already understand the consumers. 
But what is a consumer group? Well it is a group of consumers. 
So multiple consumers can form a group to share the workload. 
You can think of it like there is one big massive work, and you want to divide and assign it in smaller parts among multiple people. 
So you'll create a group and the members of the same group are going to share the burden and accomplish the bigger task together. 
That's what the consumer group is. 
Let me put up an example from the retail industry. 
In this example, we will be putting everything all together and stitching the whole story. 
It's gonna be an exciting example, so pay some attention to it. 
Let's assume we have a retail chain. 
In every store, You have a bunch of billing counters. 
You want to bring all of your invoices from every billing counter to your data center. 
Since you learned Kafka and you found Kafka to be an excellent solution to transport data from your billing locations to your data center. 
You decided to implement it. 
The first thing that you may want to do is to create a producer at every billing location. 
These producers are going to send the invoices as a message to the Kafka topic. 
The next thing that you want to do is to create the consumer. 
The consumer is going to read the data from your Kafka topic and write it to your data center. 
Simple, I'm not assuming complex processing here. 
All we want to do is to bring the data to the data center as quickly as we can. 
This whole thing looks like a perfect and straightforward solution. 
Right? But there is a small problem. 
Think of the scale. 
You have hundreds or maybe thousands of producers pushing data into a single topic. 
How do you handle the volume and velocity at the broker? But you learned Kafka exceptionally well, So you decided to create a large Kafka cluster and partition your topic. 
Correct? So your topic is partition and distributed across the cluster. 
Now every broker has got a topic partition so it can take the data from a producer and store it in the partition. 
So on the data source side you have hundreds of producers and a bunch of brokers to share the workload. 
Right? But what does it mean. 
It means the topic partitions are not only a solution to increase their storage capacity but also a method to distribute the workload. 
Kafka topic partitions are the core idea of making Kafka are distributed and a scalable system. 
You will learn more about it. 
But at this stage, I just wanted to stress on one thing. 
Partitions are the most valuable concept to understand how Kafka behaves as a distributor and a scalable platform. 
Remember it. 
Great. 
So we made our solution is scalable from the left side. 
What about the right side? We have a single unfortunate consumer. 
How is it going to handle so much incoming data alone? There comes the consumer group. 
You'll create a consumer group, start multiple copies of the consumer application in the same group, and let them divide the workload. 
Simple isn't it. 
Well that's good. 
But how do we divide the work? That's not a difficult question, right? Let's say I have 500 topic partitions. 
And I'm starting hundred consumers in a group. 
So each consumer will take five partitions and altogether they will be processing 500 partitions. 
Right? We will monitor the load for some time and see if a single consumer can handle five partitions. 
If not we can start some more consumers in this same group. 
We can go up to 500 consumers so each consumer will have just one participant to read, right? If you followed this example correctly, you would clearly understand that the topic partition is a tool for scalability and you should also notice that the maximum possible parallel consumers are limited by the number of partitions in that topic. 
Kafka doesn't allow more than one consumer to read and process data from these same partitions simultaneously. 
And this restriction is necessary to avoid the double reading of records. 
Great. 
I hope you learned the most critical core concepts of Apache Kafka. 
And now, you are familiar with the terminology that is going to be extensively used in this course. 
See in the next lecture. Keep learning and keep growing.