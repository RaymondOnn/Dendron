---
id: an591fh2vzulwhfrlfwzlhu
title: 22_SparkSchedulers
desc: ''
updated: 1700315549814
created: 1700315243680
---
Welcome Back.

In the earlier video, I talked about the following types of Spark scheduling.

I already explained the first one.

In this video, I will talk about scheduling within a Spark application.

We already know that the Spark application runs multiple jobs.

Within a single Spark application, each of your actions triggers a spark job.

We already learned it.

Right?

In a typical case, these spark jobs run sequentially.

What does it mean?

I mean, the spark will start Job 1 and finish it.

Then only Job 2 starts.

That's what I mean by running Spark jobs in a sequence.

However, you can also trigger them to run parallelly.

Let's try to understand it with an example.

Here is an example code.

What am I doing here?

I am reading two data sets.

The first one is df1, and the second one is df2.

Then I joined d1 with df2 and took the count.

Super simple.

Right?

But I am also doing a similar thing with two more data sets.

I am also reading dataframe df3 and df4.

I am again joining df3 with df4, taking the count, and printing it.

The point is straight.

I am doing two sets of independent operations.

The first set of work will read df1, df2, join them together and trigger a count() action.

Similarly, the second set of work will read df3, df4, join them together and trigger another count action.

These two joins and counts are independent.

The first set of work has nothing to do with the second set of work.

They do not have any dependency on each other.

But my code will run them in a sequence.

Let me run it and show you the jobs.

Here it is.

So we ran six jobs starting from job id zero to job id five.

Now you look at the submitted timestamp.

Job zero started at 16:26:43, and it took 2 seconds to complete.

Job one started two seconds later when job zero finished.

This guy started at 16:26:45, which is exactly two seconds of the run time of the job zero.

If you look at the other jobs, they all started one after the other.

Right?

So Spark will run your jobs in sequential order, one after the other.

Now let's come back to our code.

I have these two independent code blocks.

I prefer to run these two blocks in parallel.

Can we do it?

Yes, we can.

All we have to do is create two parallel threads

and trigger each of these blocks from two different threads.

I have to change the code and implement multithreading in my application.

Here is the new code.

So what am I doing?

I created this do_job function.

This function takes two input parameters.

These parameters are nothing but the data file names.

So I will read the first data file into df1, then the second data file into df2, join these two data frames and count it.

That's what we were doing earlier in two code blocks.

Right?

Now I have a common function for doing that.

Then I come back to my main method and run this loop.

The for loop runs twice.

Every iteration will create one thread.

The target for the thread is the same do_job function.

But I am changing the file names for each iteration.

So we are creating two threads here.

Finally, I will start both the threads and then wait for both the threads to complete.

That's all.

This code is designed to run the do_job function in two different threads,

and both the threads will trigger a count() action.

Right?

Now let me run it and see what happens with my spark jobs.

Here it is.

We still have six jobs starting from job id 0 to job id 5.

But now, things are running in parallel.

Job zero and job one started in parallel at the same time.

Job four and job five also started in parallel at the same time.

So what does it mean?

Your Spark application runs as a set of spark jobs.

Each Spark job represents an action.

You can submit these actions sequentially, or you can also submit these actions using parallel threads.

Right?

But when you run spark jobs in parallel, you have to think about resourcing.

I mean, How will spark allocate executor slots for these parallel jobs?

Because each action or a job runs as a set of parallel tasks.

So each job requires some resources to run multiple tasks in parallel.

When you create a multithreaded spark application to submit two or more parallel jobs,

they all need resources for their corresponding tasks.

Right?

And that causes competition between jobs to acquire resources.

How do we handle it?

This is what we mean by scheduling within an application.

If your application is a single-threaded spark application, you do not worry about the scheduling within the application.

But if you create a multithreaded Spark application

and submit jobs from multiple parallel threads, job scheduling becomes an important part.

So how do we handle it?

Let me explain.

By default, Spark's job scheduler runs jobs in a FIFO fashion.

Each job is divided into stages, and the first job gets priority on all available resources.

So if the first job has some stages and tasks, it will consume as many resources as needed.

Then the second job gets priority.

So the point is straight.

If the jobs at the queue head don't need to use some resources, the next job can start to run.

But if the jobs at the head of the queue are large, then later jobs may be delayed significantly.

However, you can change the FIFO scheduler and configure spark to use the FAIR schedular.

Here is the configuration.

Under fair sharing, Spark assigns tasks between jobs in a round-robin fashion.

What does it mean?

Simple!

Assign one task in a slot from the first job, then assign one task in a slot from the second job, and so on.

In this approach, no parallel jobs will be waiting for resources,

and all get a roughly equal share of cluster resources.

Make sense?

Great!

Once you enable FAIR schedular, you can see the list of fair scheduler pools in your spark UI.

Here is an example screenshot.

You can see it here.

We are using the FAIR schedular default pool.

And within the pool, we have a FIFO schedular.

You can go further down to create and configure multiple pools within the FAIR schedular,

but that may not be necessary.

Fair schedular with default pool works well enough.

Great!

That's all about the job scheduling in Apache Spark.

See you again.

Keep Learning and Keep growing.