---
id: x0uh9yqhir3wqviq5ge4bb9
title: 13_ConfigDevIDE
desc: ''
updated: 1699880890649
created: 1699880860117
---

Welcome back.

This training includes several examples and demonstrations.

If you are willing to follow along and try those examples yourself, you will need to set up your IDE.

I am using IntelliJ IDEA as my development IDE, and in this lecture, I'm going to explain the step

by step process to set up precisely the same environment which I've done for all the demos in this course.

Let us start.

We will be using the Confluent Kafka Community edition in this course.

You already learn to install and configure three-node Confluent Kafka.

That is what we are going to use.

However, the earlier configuration is going to create log files in the /tmp/ directory.

This configuration is perfectly fine.

However, we will be running multiple examples and using the same Kafka cluster installation for all the

examples.

Sometimes, this approach is not convenient for local development.

So I want to use a separate data directory for each example.

How can you do that?

Well that's super simple.

Change the data log directory to the current directory, and every time you start the Kafka cluster from

your IDE, it will create new logs in the current directory.

It works like running separate Kafka cluster for each example.

Let me show you.

Open your zookeeper.properties file.

Place a double dot at the beginning of the data directory configuration. Save it. Now do the same with

your server.properties file.

Repeat it for all the three configuration files.

Great.

I will execute an example from the IDE and show you the effect of this change.

But before that, let me set up an environment variable. To avoid typing absolute part all the time,

I'm going to set the KAFKA_HOME environment variable. Here is the command.

This command works on Windows.

However, if you are using Linux or Mac, you should be using the appropriate command for your operating

system.

Good.

So now, we are ready to start the IntelliJ IDEA and open one example project.

I'm assuming that you already have IntelliJ, and you know how to install it and create Java Projects

using IntelliJ. Download

the example projects from your lecture resources and uncompress it. Y ou should see a directory structure

similar to what I'm showing here.

Now

you can start IntelliJ and open the example project.

All the examples and this course will include some scripts to start Kafka

services, create topic and other things which are needed to run the example.

The current example has got these many scripts. All these scripts are windows batch files.

Let me show you.

So this is scripts to start the zookeeper.

You already know the command, right?

So we are using KAFKA_HOME environment variable and then navigating to bin\windows and starting the

batch file to run the zookeeper.

Similarly, we are specifying the zookeeper properties file location using the environment variable. These

scripts will work on Windows machine without any change.

However if you are on Linux or Mac machine, then you should modify these scripts.

For example, if I'm running it on Linux then I'll be using $KAFKA_HOME.

I might also need to change this slash. And the dot bat extension will go away.

You should also make sure that these shell scripts have got execute permission, right?

You should be already knowing all these simple things.

Great!

Now let me execute it and show you the main thing that I wanted to show.

Let me start

the zookeeper server.

I can

start it from the IDE itself, and the output will come in a docked windows

inside the IDE itself.

Here it is.

So my zookeeper is now running.

Let me start the first Kafka broker.

If you do not see this run menu option in your IntelliJ, then you might have to install a batch script

support plugin.

Here it is.

I've got it and I also recommend that you should also get this installed.

Start the first Kafka broker.

Then start the second one.

The third one.

Now let me create a topic.

Do you see this tmp directory?

This is the home for Kafka and zookeeper data.

I got it in the current directory.

Now I can run my example, send some data. Process it and play it the way I want. All the data for this project

remains in my current directory of this project.

When I start another project, the log files for the other project remains isolated in the current directory

of the other project.

Right?

When I'm done with this project or if I want to clean up and restart things fresh, I'll stop all services

and build this tmp directory.

Simple, right?

This simple approach makes life quite comfortable during the development phase.

That's all for this video.

See you again.

Keep learning and keep growing.