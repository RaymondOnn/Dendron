---
id: b9u1o8wwnkr42gmupirdzjw
title: 03_ConnectCoreConcepts
desc: ''
updated: 1700013780967
created: 1699880307340
---
Welcome back to Kafka big picture for Beginners. 
In the earlier lecture, I talked about the five core components of the Kafka ecosystem. 
I also talked about Kafka brokers Kafka producers, and Kafka consumers. 
So, we covered an overview and core concepts for the first two items. 
In this lecture, I'll be explaining some fundamental constructs for Kafka Connect. 
This lecture will give you a clear answer to the following questions. 
Let's start with the first question. 
What is Kafka Connect? We already learned in the earlier lecture that Kafka was initially developed at LinkedIn to solve the data integration problem. 
Here is the diagram that I showed you. 
The point is a straightforward, we cannot produce a single standalone application that does everything in an enterprise. 
Why? Because that's practically impossible and not economical. 
So in a typical enterprise, you may have a bunch of independent and running applications. 
Some of them are custom designed and developed in-house. 
Others may have been purchased from a third party application vendor. 
Few of the applications maybe running outside of the organization boundary and maintained by the partners or the service providers, right? They all might be generating some data and owning it. 
However, they also need some additional data which is created and owned by other systems. 
For example, financial accounting software needs data from an invoicing system. 
An inventory management system needs data from invoicing, warehouse, and shipment. 
Analytics would need data from all over the enterprise, right? So data integration is a common problem. 
You start solving it, and then some time it becomes a messy network of data pipelines. 
When you reach that stage, it becomes almost impossible to maintain it. 
Linden created and used Kafka to simplify these pipelines. 
So how they did it? Let's look at a simplified version of the problem. 
You have an application here, and this could be an invoicing application or whatever. 
But we are going to assume that the app has a backend database where it maintains all the generated data. 
Now we have a requirement to bring some of the data from here to the Snowflake data warehouse. 
How do you do it? You decided to use Kafka as a broker. 
Why? Because Kafka will keep your data integration simple. 
Bringing data from your invoicing system to the Kafka broker is a one time activity. 
Once it is in the Kafka Cluster, you can bring it to the Snowflake. 
If you need to move the same data to other applications, they can also consume it from the broker. 
All these pipelines are going to be of one to one link. 
Right? Now, assume what will happen if you remove the Kafka. 
You may have to build one too many pipelines and I'm sure you're invoicing applications cannot handle so many connections and workload. 
Great! So you know you are going to use Kafka broker. 
But how do you bring data from your invoicing application to the Kafka Cluster. 
Do you want to create a producer? Let's try and evaluate that option. 
There are two ways to create a produce depending upon the source code availability of your source system. 
If you have the source code of this invoicing application and it is practically feasible to modify your source application. 
Then you can create an embedded Kafka producer using Kafka producer APIs. 
The embedded Kafka producer becomes part of your source application, it runs inside the application and sends invoices to the Kafka Cluster. 
Simple and straightforward, right? But what if you do not have the source code of your invoicing application. 
Or you realized, for some reason, it is not practical to modify your data source application. 
Then what? How do you send data? You can create an independent Kafka producer for reading and writing. 
On one side, it connects to the source application database, reads the data, and sends it to Kafka Cluster on the other side. 
Simple, isn't it? Both options are good. 
It's up to you to evaluate these options and make a decision. 
But if you happen to settle down to the second option of creating an independent producer then you are signing up to solve a problem that might have already been solved for you. 
Yes you are right. 
That's what Dick Kafka connect is designed for so the Kafka Connect is a system which you can place in between your data source and decaf Cluster. 
Then all you do is to configure it to consume data from this source system and send it to the Kafka Cluster. 
You do not need to write a single line of code. 
Everything is already done and made available to you. 
You'll just configure and run, and the Kafka Connect will do the job for you. 
Looks good. 
But we are not done yet. 
We also need to bring data from the Kafka Cluster to these Snowflake data warehouse. 
So, what can you do? Place the Kafka connect between these two systems, configure it, and the Kafka Connect will read data from the Cluster and write it to the Snowflake database. 
Simple! isn't it? The left side of the connector is called Kafka Connect Source connector. 
And the right side of the connector is called a Kafka Connect Sink Connector. 
We use the source connector to pull data from a source system and send it to the Kafka Cluster. 
And guess what? The Source Connector will internally use the Kafka producer API, right? Similarly, we use the Sink connector to consume the data from Kafka topic and Sink it to an external system. 
These Sink connectors will internally use the Kafka Consumer API, right? That's what the Kafka connect is. 
I hope you got your answer - What is Kafka connect? Kafka Connect is a component of Kafka for connecting and moving data between Kafka and external systems. 
We have two types of Kafka Connectors. 
Source Connector and Sink connector. 
And together, they support a bunch of systems and offer you an out of the box data integration capability without writing a single line of code. 
Now the next question is this, how do they provide out of the box Source and Sink. 
Well, this question is obvious. 
We learn that you could place the Kafka connect in between your source or target system and the Kafka Cluster. 
Then you configure it. 
And Kafka Connect will take care of copying data from one to another. 
You don't have to write a single line of code. 
How is it possible? I mean, there are a bunch of different systems. 
For example we have relational databases data warehouses like Teradata, IoT Hubs, Salesforce, Twitter, Reddit, Local File system directories, Cloud storage, Hadoop Storage, Elastic Search, Cassandra, MongoDB, Google Firebase and many more. 
All of them are different systems. 
Reading and writing to these systems have a different mechanism and considerations. 
How is it possible for Kafka connected to this copying without writing some code? Well it is possible if someone else has already written it for you. 
Let me explain. 
Kafka Developers made a smart decision and created a brand new framework for implementing Kafka connector. 
They named it Kafka connect framework and open-sourced it. 
The Kafka connect framework allows you to write connectors. 
These connectors are implemented in two flavors. 
Source connector and Sink connector. 
The Kafka connect framework takes care of all the heavy lifting, scalability, fault tolerance, error handling, and bunch of other things. 
As a connector developer, all you need to do is to implement two Java classes. 
The first one is SourceConnector or SinkConnector class. 
And the second one is the SourceTask or the SinkTask. 
There are many other details to it, but that's what we do at a high level to create a Kafka connector. 
Once your Connector is developed and tested, you can package it as a Uber Jar or as a Zip archive. 
Share it with others, and they should be able to use it. 
And that's what all this source and target system vendors have been doing. 
So assume you want to bring some data from an RDBMS to a Kafka Cluster. 
All you need to do is to take an appropriate source connector, for example a JDBC source connector. 
Then you install it in your Kafka connect, configure it and run it. 
That's all. 
The JDBC Connector will take care of the rest. 
Similarly, when you wanted to move data from your Kafka Cluster to your Snowflake database, get these Snowflakes sink connector, install it in your Kafka connect, configure it, run it, and boom. 
You are done. 
Now, the next question is this - Can we scale Kafka Connect? We learned producer/consumer scalability in the earlier lecture. 
We were able to scale the producer by adding more producers to send data in parallel. 
We scaled the Cluster by simply adding more brokers. 
We also partitioned the Kafka topic. 
Similarly, we scaled the consumers by adding more consumers in the consumer group. 
What about Kafka Connect? How do we scale it? Well that's a straightforward. 
The Kafka Connect itself is a Cluster. 
Each individual unit in the Connect Cluster is called a Connect Worker. 
You can think of it as a group of computers, each running one Kafka Connect Worker. 
On the left side scenario, you can have a bunch of SourceTask running here to share the workload. 
For this example, one task might be pulling data from one table in the database, and the other task might be pulling data from another table. 
You can configure all these things and play with them the way your connected developer has designed and allowed. 
Similarly, on the right side, you can have a bunch of SinkTask running here to share the workload. 
So, nothing to worry about the scalability. 
You can play with the number of tasks and scale the Cluster capacity by adding more workers. 
You might be wondering about this figure. 
Do we need to Kafka connect Clusters? One for source and another one for Sink? No! Absolutely not. 
You can have one Kafka connect Cluster and run as many connectors as you want. 
In this example, we can have one source connector and one Sink connector running in the same Kafka connect Cluster. 
If your Kafka connect Cluster it still has some available capacity, you can install a salesforce connector and start one more connected in this same Cluster. 
If your Cluster is fully utilized, you can scale it by adding more workers to the same Cluster. 
And you can do it dynamically without stopping any existing connectors. 
What is next? Does it merely copy the data? Can we perform some processing or transformations? Kafka Connect was designed to perform a plane copy our data movement between third party systems and Kafka. 
In both the cases (Source or Sink), one side must be a Kafka Cluster. 
However, Kafka connect also allowed some fundamental Single Message Transformations (SMTs). 
What it means - You can apply some transformations or changes to each message on the fly. 
And this is allowed with both source and Sink connectors. 
Here is a list of some common SMTs. 
Add a new field in your record using a static radar metadata. 
Filter or rename fields. 
Mask some fields with a Null Value. 
Change the record Key. 
Route the record to a different Kafka Topic You can chain multiple SMTs and play with it to restructure your records and route them to a different topic. 
However, these SMTs are not good enough to perform some real life data validations and transformations. 
Now let's come to the concluding part. 
- Kafka Connect Architecture How does it work? To learn the Kafka connect architecture, you have to understand three things. 
Worker, Connector and Task. 
You already learned that Kafka Connect is a cluster, and it runs one or more workers. 
So let's assume you started a Kafka Connect Cluster with three workers. 
These workers are fault tolerant, and they use the Group ID to form a Cluster. 
This Group ID mechanism is the same as Kafka Consumer Groups. 
So, all you need to do is to start workers with the same group id, and they will join hands to form a Kafka Connect Cluster. 
These workers are the main workhorse of the Kafka Connect. 
That means, they work like a container process, and they will be responsible for starting and running Connector and the Task. 
These workers are fault-tolerant and self-managed. 
What does it mean? Well, that's a simple meaning. 
If a worker processes stops or crashes other workers in the Connect Cluster will recognize that and reassign the connectors and tasks that ran on that worker to the remaining workers. 
If a new worker joins a connect Cluster, other workers will notice that and assign connectors or tasks to it and make sure the load is balanced. 
So, in a nutshell, these workers will give you reliability, higher availability, scalability and load balancing. 
Now all you need to do is to copy the data. 
Let's assume I wanted to copy data from a relational database. 
So, I'll download the JDBC Source Connector, install it within the Cluster. 
Well, the installation is to make sure the JAR files and all its dependencies are made available to these workers, right? There is nothing anything special about the installation. 
The next thing is to configure the connector. 
So the configuration means providing some necessary information. 
For example, database connection details, a list of tables to copy, frequency to pull the source for the new data, the maximum number of tasks, and many other things depending upon your Connector and the requirement. 
All this configuration goes into a file, and you will start the connector using some command line tool. 
Kafka Connect also offers you REST APIs so you can even begin the connector using the REST API instead of the command line tool. 
At this stage, one of the workers will start your Connector process. 
I told you workers are like a container. 
They start and run other processes. 
So this time, it is that connect the process. 
Now the connector process is mainly responsible for two things. 
The first thing is to determine the degree of parallelism. 
That means how many parallel tasks can I start to copy the data from this source. 
You cannot answer this question if you do not know the splitting mechanism, right? So the first thing is to decide how to split the data copying work. 
For example, let's assume I wanted to ingest data from five tables. 
So, I listed five tables in the configuration side and it started the JDBC connector. 
Now it is quite natural for the connector to detect the splitting mechanism. 
One table per task, right? So the maximum number of parallelism is five in this case. 
However, the splitting logic for different source systems is written in the connector code, and you give configuration options accordingly. 
So you must know your Connector and configure it accordingly. 
Great! So, the connector knows that it can start five parallel tasks and assign one table to each task of copying data from the source system. 
Remember, the connector is not going to copy the data. 
It is only responsible for defining and creating a task list. 
Each task will be configured to read data from an assigned list of tables. 
In our example, it is just one table. 
The Connector will also include some additional configurations such as database connection details and other things to make sure that the task can operate as an independent process. 
Finally, the list of tasks will be given to these workers, and they will start the task. 
So your task is distributed across the workers for balancing the Cluster load. 
Now the task is responsible for connecting to the source system, polling the data at a regular interval, collecting the records, and handing over it to the worker. 
Yes, that's correct. 
They do not send the record to the Kafka Cluster. 
That task is only responsible for interacting with the external system. 
This source task will handover the data to the worker, and the worker is responsible for sending it to the Kafka. 
In the case of the Sink task, they get the Kafka record from the worker, and the task is only responsible for inserting the record into the target system. 
Why is it designed like this? Well, that's the reusable design. 
Reading and writing data to a Kafka Cluster is a standard activity. 
So it is taken care of by the framework. 
We have two things that are changing for different source and target systems. 
How to split the input for parallel processing. 
This is taken care of by the Connector class. 
How to interact with the external system. 
This is also taken care of by the Task class. 
And these are the things that are connector developer needs to take care of. 
Most of the other stuff like interacting with Kafka, handling configurations, errors, monitoring connectors, and tasks, scaling up and down, and handling failures are standard things and are taken care of by the Kafka Connect Framework. 
Makes sense? That is all for this lecture. 
See you in the next lecture. 
Keep learning and keep growing.

