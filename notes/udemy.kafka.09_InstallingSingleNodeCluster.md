---
id: czit8aagnwmfm6e20qxixz5
title: 09_InstallingSingleNodeCluster
desc: ''
updated: 1699880675650
created: 1699880667486
---
Welcome back.

In this lecture, we are going to set up a single node Kafka on your local machine.

But before that, let me add some contextual theory.

Kafka comes in many flavors, and you can classify them into three categories.

The first one is the open source version of Kafka.

You can download it from the Apache website, install it, use it and manage it yourself.

If you're getting into some operational issues or open bugs, need to upgrade Kafka versions, or facing

some infrastructure problems.

You are all alone and expected to develop in-house expertise to handle it.

The point is straight. Your organization is unlikely to use open source distribution unless you have

a bunch of Kafka admins and infrastructure experts who understand the in and out of Kafka cluster. And

that's hard to find.

The second option is to get a commercial distribution that comes with a bunch of tools and utilities

to manage your day to day operations and monitor your cluster. Your commercial vendor is going to employ

a bunch of well-trained and highly skilled support professionals for you.

This option comes with a cost to your organization.

However you are most likely to work on these kinds of setups. Confluence Kafka is one such commercial

distribution, and it is the most popular one.

Conflict also offers a community addition without any cost.

And that's what we are going to use in this course.

Why Confluent Kafka and Why not Apache Kafka?

You already know the answer. Because you are most likely to see conflict Kafka in your production environment.

So we are going to use the same for our learning.

The third option is a fully managed Kafka service in the cloud. In this option,

you do not need to download, install, run operate or maintain anything related to the Kafka cluster.

Just use the cluster for producing and consuming data. All the infrastructure headache is taken care

of by the managed service provider.

This option is the simplest way of using Kafka for your projects and comes with different payment plans.

Confluent is also a managed service provider, and there are others as well including Amazon and

Aiven.io.

However, you should also notice that all these options are to get a Kafka cluster.

You still need to develop applications for creating and processing data streams.

Great.

So with that background set, let's download Confluent Kafka community edition and setup it on your local

machine.

Go to the confluent.io and hit the download button. Scroll down, and you will see a link to download the

community edition. Uncompress the download file, and you should see a directory with the following

content. And that is all. Your download is a pre configured single load Kafka cluster.

Since Kafka is a JVM based application, all you need to make sure that you have got Java installation

on your machine. And you are ready to start your Kafka services. You can check your Java installation

using the following command. Kafka

comes with a bunch of command-line tools, and you can find them in your bin directory.

Let me show you.

These are all shell scripts. And you can find the compatible windows batch files in the windows directory.

I'm on windows machine so I'm going to use Windows batch files.

If you are doing it on Linux or Mac, then you should be using these shell scripts.

Great.

So we are all set to do the real thing that we wanted to do in this video.

Start a single node

Kafka cluster, right?

Let's do it.

Starting a Kafka cluster is a two step process.

The first step is to start a zookeeper server and you can start using the zookeeper-server

-start script. However,

this script also takes one mandatory argument.

A zookeeper configuration file.

I talk about the configuration file in a later video, but your download already comes with the ready

to use zookeeper configuration file.

configuration file. You can find it in etc\kafka\ directory, and the file name is zookeeper.properties. Oops!

If you get the same error, then your confluent Kafka download has got a broken script for windows.

Let's fix it. Open Kafka-run-class.bat file. Search for text "Classpath addition for the core."

Copy paste the following content above the selected line.

Save the file and try again.

Great.

So your zookeeper is now running. The next thing is to start Kafka broker. Minimize the zookeeper

window and start a new command window.

You're gonna start Kafka broker using Kafka server to start a script.

This one also needs a configuration file which you can find in the,

etc\kafka directory.

Let's run it.

Great! Kafka

server ID 0 started.

That's your Kafka Broker ID.

You are now ready to play with this single node Kafka cluster.

We are done.

Do you have a question.

I have one.

What is zookeeper, by the way?

We knew Kafka brokers,

but where did the zookeeper come in between.

So zookeeper is a kind of database where Kafka brokers would stored a bunch of shared information.

And it is used as a shared system among multiple Kafka brokers to coordinate among themselves for various

things.

Kafka community has already announced that they are going to retire the zookeeper in the coming days.

But until then, Kafka needs zookeeper for coordinating things among the brokers.

And you must have it running even if you have got a single broker. More on zookeeper and its exact

role will come in a later video.

Great!

So we have seen the Confluent Community edition.

However, if you wanted to use the open source edition, you can do that as well.

The steps are almost the same, with minor differences.

Let me quickly demonstrate that well.

Before we start working with another version of Kafka, let me shut down the current version.

If you don't do this, you are going to face port conflict issues because the open-source version is also

going to use these same ports.

Great!

Go to the Apache Kafka website. Hit the download button.

We are looking for a binary download. Click any one of these, and your download should start. Uncompress

your downloaded file, and you should see the following directory structure.

The directory structure looks similar to Confluent Kafka, right?

There are few differences, and you will notice that some directories are missing from the open-source version.

But nothing to worry.

The open source version also carries this same thing.

It is just the directory structure that is different, and confluent brings a few additional capabilities

that you are going to understand as we progress with the course.

So, what do we want to do?

Two things. Start zookeeper, and then Start Kafka broker.

That's what we have done with Confluent Kafka, right?

Let's start the Zookeeper.

Okay.

So here is the command which we used to start this zookeeper.

This command remains the same except for the zookeeper configuration file location.

Let me change it.

That's all.

Let me start

the zookeeper. Great! It is running now and listening to port 2181.

That's the default zookeeper port.

Let me start a new command window and start the Kafka broker. So, here is a command.

This one also remains the same except the broker configuration file location.

Let me change it. Good. Hit the enter key, and it should start the Kafka

Broker. Started.

Right?

So in this lecture, we learn to start a single note Kafka broker using two different types of distributions.

Confluent Community Edition and Open Source Edition.

Once you have the zookeeper and Kafka running, leave these windows open, and you are ready to start using

Kafka cluster for all your hands-on activities.

If you are willing to explore the Kafka managed services, you can try accessing Confluent Cloud or Aiven

Kafka.

That's all for this lecture.

See you again in the next video.

Keep learning and keep growing.