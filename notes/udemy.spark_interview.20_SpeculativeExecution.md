---
id: xek8wnyde55qm82fxxn8ect
title: 20_SpeculativeExecution
desc: ''
updated: 1700315494941
created: 1700315211360
---
Welcome Back.

In this video, I am going to talk about Speculative Execution in Spark.

So let's start.

We already know that Apache Spark runs your application as a set of jobs.

These jobs are broken down into stages, and each stage is executed in parallel tasks.

So a task is the smallest unit of work in Apache Spark.

Right?

Now let's assume your Spark job is running ten tasks.

You looked at the Spark UI and noticed that out of those ten tasks.

All other tasks take almost equal time to complete except one task.

One task takes a much longer time to complete.

However, a Spark job stage is not complete until all the tasks of that stage are complete.

So looking at this diagram, you realized that one task is delaying your stage.

If this task is also completed along with other tasks,

your stage will complete faster, and you can reduce the Spark job execution time.

So what can you do?

One easy thing is to enable Spark Speculative execution.

You can enable it using the following configuration.

By default, Spark speculative execution is set to false.

You can set it to true and enable it.

Once you enable speculative execution,

Spark will automatically identify the slow-running tasks and run a duplicate copy of the same tasks on other nodes.

The idea is to start a copy of the same task on another worker node and see if that runs faster.

This new duplicate task is known as a speculative task.

Now you have two copies of the task.

So Spark will accept the task that finishes first and kill the other task.

These speculative tasks can solve your problem in some cases.

However, speculative tasks are not silver bullets, and they do not solve every slow-running task problem.

They are helpful if and only if the task is slow due to a problem with the worker node.

What does it mean?

Simple!

Your task may be running on a faulty worker node,

performing slowly due to some hardware problem, or struggling with overload.

It could be anything specific to the worker node that is causing your task to run slow.

If we restart that task on a different worker node, it might run faster.

Right?

And that's the main idea behind the Spark speculative task execution.

Spark will automatically identify a slow-running task,

run a duplicate copy of the same on a different worker node, and see if that runs faster.

Finally, Spark will take only one faster task from these two and kill the other one.

However, you should also remember that enabling speculative execution

will start taking some extra resources from your Spark cluster.

Running speculative tasks is an overhead, and in many cases, it might not give you any benefits.

That's why it is disabled by default.

A speculative task cannot help you in the following situation.

If your task is running slow due to a data skew problem

It is slow because the task is struggling with low memory.

Spark doesn't know the root cause of the slow-running task,

and it simply starts a speculative task to see if that one finishes faster.

So be careful before enabling speculative execution.

If you have some extra resources, enabling speculation might be a good thing to do.

Spark also offers you some additional configurations to fine-tune the speculation.

Here are they.

The default value of the speculation interval is 100 ms.

So Spark will check for task slowness and see if a speculative task is needed.

And Spark will perform that check in a 100 ms loop.

The default value of the speculation multiplier is 1.5.

So a task is speculative only if it takes more than 1.5 times the median of other tasks' execution time.

So if the median value of other tasks is 5 seconds,

then a task can be speculative if and only if it takes more than 7.5 seconds.

Make sense?

The third configuration represents a fraction of tasks

that must complete before speculation is enabled for a particular stage.

The default value is 75%.

So if more than 75% of tasks are complete and one task is still struggling,

Spark will consider starting a speculative task.

The next one is the minimum amount of time a task runs before being considered for speculation.

This can be used to avoid launching speculative copies of tasks that are very short.

The last one is a hard limit of task duration, after which the scheduler would try to speculative run the task.

Great!

So Spark understands that the speculation consumes extra resources,

it is a costly solution, and it may not solve the problem of slow-running tasks.

And hence, they offer you a bunch of configurations to fine-tune the speculation

so you can protect yourself from running unnecessary speculative tasks.

Make sense?

Great!

That's all about Spark Speculative execution.

See you again.

Keep Learning and Keep Growing.