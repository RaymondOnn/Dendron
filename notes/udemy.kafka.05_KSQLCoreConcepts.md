---
id: vqdhf8dihv31ndazbnnl6r7
title: 05_KSQLCoreConcepts
desc: ''
updated: 1699880493375
created: 1699880470292
---
Welcome back to Kafka big picture for absolute bigness.

The last item in our list is the KSQL. In this lecture,

I'm going to give you a quick overview of the KSQL, and you are going to get answers to the following

questions.

So let's start with the first question.

What is KSQL?

KSQL is an SQL interface to the Kafka Streams.

So in very short, most of the things which you can do using Kafka Streams are available to you in KSQL.

It means you can create scalable and fault-tolerant stream processing workloads without the need

to write code in a programming language such as Java or Scala. KSQL has got two operating modes.

Interactive Mode and Headless Mode. Interactive mode is using a command-line interface (CLI) or a

web-based UI to submit KSQL and get an immediate response.

The CLI works like any database SQL interface would work.

The Headless mode is a non-interactive mode that allows you to submit your KSQL files, which are

executed by the KSQL server.

The headless mode is ideal for the production environment, whereas the CLI mode is ideal for the development

environment.

Now you know what is KSQL,

then let's look at the KSQL architecture.

How does it work?

The KSQL comes with three components.

KSQL engine, REST interface and KSQL CLI/UI. The KSQL engine and the REST interface together

form the KSQL server. The KSQL server can be deployed in one of the available modes. Interactive

mode and Headless Mode.

You can also deploy multiple KSQL servers to form a scalable KSQL cluster.

However, all servers that run in a cluster must use the same deployment mode.

The KSQL engine is the core component which is responsible for KSQL statement and queries. Under

the hood, the engine is going to parse your KSQL statements, build corresponding Kafka streams topology,

and run them as streams tasks.

And these are streams tasks are executed on the available KSQL servers in the cluster.

You can dynamically add more servers in the cluster to scale out the resources, and fault

Tolerance is an inherent feature of the Kafka streams.

I hope I don't need to mention that the KSQL cluster is separate from your Kafka cluster, and your

KSQL Server will internally communicate to the Kafka cluster for reading inputs and writing outputs.

The REST interface is to power the KSQL clients. So, the KSQL CLient will send the commands

to the REST Interface, which will internally communicate with the KSQL Engine to execute your KSQL

Commands.

Now the next question - What can you do with KSQL.

In a nutshell, KSQL allows you to use your Kafka topic as a table and fire SQL like queries

over those topics. With that power in your hand,

You can imagine the kind of things that you can do.

Here are some examples. You can use

group by and aggregates on your Kafka topics.

You can group an aggregate over time window.

You can apply filters. You can join two topics. You can sink the result of your query into another topic.

The possibilities are endless, and all you need to know and learn is the same SQL and some Kafka

flavor to it.

So the KSQL for Kafka is one big step forward for Kafka to become a real time data warehouse.

Those days are not too far when you might see JDBC/ODBC connectors being available for KSQL

and visualization tools like Tableau and QlikView to start connecting with the KSQL.

I'm not predicting anything, but that's the next logical step for Kafka and KSQL.

Great.

That's all for this lecture.

See you in the next lecture.

Keep learning and keep growing.