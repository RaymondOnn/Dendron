---
id: 558acegok9cxtly5em35ure
title: 21_DynamicResourceAllocation
desc: ''
updated: 1700315530198
created: 1700315229945
---

Welcome Back.

In this video, I am going to talk about Spark Scheduler.

So let's start.

When we talk about scheduling in the context of Apache Spark, we mean two things.

Scheduling Across Applications

and Scheduling Within an Application

So let's try to understand the first scenario of scheduling across applications.

Spark is a distributed application, and it runs on a cluster.

Right?

So we submit an application to a cluster.

The cluster manager allocates some resources, and your application starts on the cluster.

What if we submit two applications to the same cluster.

Or what if some other user also submits an application to the same cluster.

The second application will also run on the same cluster.

The point is straight.

At any time, your cluster might be running more than one Spark application.

Right?

So in a typical case, a spark cluster is shared by multiple applications.

But the question is this?

How cluster-manager allocates resources to the Spark application,

and when do they release those resources.

Let me elaborate on it.

Let's assume you have a cluster.

And the maximum capacity of the cluster is to create 100 containers.

So we cannot have more than 100 containers at a time.

Now, you started one application, and the application demands 100 containers.

The resource manager will allocate 100 containers to the first application.

What if we submit another application.

The second application is small, and it needs only five containers.

But we do not have any containers left.

So the second application must wait for some resources to become free.

Right?

That's not efficient.

A small application has to wait for a large application to finish.

And this is an area where Spark offers you some customization so you can better manage your resources.

Let me explain it.

Spark offers you two resource allocation strategies.

Static Allocation

and Dynamic Allocation

These two strategies are not for Cluster Resource managers.

These are for Spark.

They do not have anything to do with the cluster resource manager.

These two approaches define how your application requests resources

and how it releases the resources back to the cluster manager.

Let me explain these two approaches.

The static allocation is the default approach.

In this approach, as soon as your driver starts, it will request executor containers from the cluster resource manager.

The cluster manager gives as many resources as demanded by the driver.

But now, your application will hold all those resources for the entire duration of running the application.

Even if your application is not utilizing them, you will hold them with you.

So the static allocation is a kind of first-come, first reserve approach.

In this approach, the first application will demand resources and reserve them for the entire duration.

These resources are released back to the cluster manager only when the application finishes its execution.

This approach has a problem of resource utilization.

I mean, think about your application.

You created an application that runs five stages.

The first stage requires 400 parallel tasks, the second stage requires 100 parallel tasks,

and all other stages require 200 parallel tasks.

You demanded 100 executors with 4 CPU cores each.

Why?

Because you have a stage that needs to run 400 tasks.

So you need 400 slots, and hence you demanded 100 executors, each with four slots.

Your estimation is correct.

You did the right thing.

But your application runs in five stages.

Only one stage is making good use of 100 executors.

The other four stages are not using all the executors.

Right?

But you are holding all those executors till the end.

That's a problem.

How do we solve it?

That's where dynamic resource allocation comes into play.

Spark offers dynamic resource allocation, and you can enable it using the following configurations.

Dynamic allocation is disabled by default.

But you can enable it to set these two configurations to true.

Once you enable dynamic allocation, your spark application will automatically release the executors

if they are not using it.

Similarly, they can again acquire executors when they are needed.

The resource manager has nothing to do here.

It is the Spark application that demands resources when needed

and releases them if they are not required for the time being.

And your application starts doing it dynamically.

Now let me walk you through the same example.

So you need to run 400 parallel tasks in your first stage.

So you will demand 100 executors.

Right?

The cluster manager will allocate your 100 executors, and you will start running your first stage.

Once you are done with your first stage, you need only 100 slots for the second stage.

Right?

So you will release some executors back to the cluster manager.

Why?

Because you do not need them for the moment.

You finished your second stage with fewer resources.

But now you are into the third stage, and you need more slots.

So your application will again demand more slots and take them from the cluster manager.

The point is straight.

Dynamic allocation is a Spark configuration.

It allows Spark applications to release and demand resources as the requirement changes dynamically.

Make sense?

Great!

You have two more important configurations.

The default value of idle timeout is 60 seconds.

So if an executor is idle and you do not have any task running on an executor for 60 seconds,

your application will release the executor back to the cluster manager.

You can change this configuration, but 60 seconds is a reasonable value for a standard application.

The default value of this backlog timeout is 1 second.

So if you have some pending tasks for more than one second

and do not have any free executor where you can allocate your pending task,

your application will request more executors.

Make sense?

So the first configuration controls the release time for an idle executor.

And the second configuration controls the request time for more executors.

Make sense?

Great!

That's all for Spark Dynamic Resource allocation.

So we learned that the cluster manager schedules the Spark application driver,

and resources for running the application are also provided by the resource manager.

Once your application driver starts, it can keep those resources or dynamically release and request them again.

If you have a shared cluster, you should enable dynamic allocation

so your Spark applications can demand and release executors dynamically.

Make sense?

Great!

See you again in the following video.

Keep Learning and Keep growing.