---
id: tmm3pwllxiyni6fwjml7pyr
title: 12_AQEJoinOptimization
desc: ''
updated: 1700315293188
created: 1700314968060
---

Welcome Back.

In the earlier video, I talked about the three features of Spark Adaptive Query Execution.

Here they are.

I also covered the Dynamically coalescing shuffle partitions.

In this lecture, I will talk about the Dynamically switching join strategies.

So let's start understanding the problem first.

Why do we need dynamic join optimization?

Let's assume you have two large tables.

And you are joining these two tables using the following query.

I am showing a Spark SQL here, but you might be doing it using an equivalent dataframe expression as shown here.

Both are the same.

Both of your tables are large tables, so you are expecting a sort-merge join to take place.

You ran your job and checked the execution plan.

Here it is.

So what do you see?

I am reading two tables, and I have two stages of reading those tables.

So here is my first stage,

and this one is the second stage.

Both these stages send data to exchange.

And everything after the exchange is part of the third stage.

So my third stage collects the data from the exchange,

performs the sorting operation on the data, and finally joins them.

Right?

That's what happens in a sort-merge join operation.

Do you see any problems or opportunities to optimize this operation?

Let me show you.

I am assuming both of my tables are large enough.

But I am also applying a filter condition on the large_tbl_2.

The large table_2 is a 100GB table, but what is the effect of applying a filter?

How many rows am I selecting post filter?

You did some investigation and realized that you selected only 7 MB of data from the large table_2.

Now think about the situation once again.

Your large_table_1 is 100 GB, and you are selecting all rows from this table.

Your large_tbl_2 is also 100 GB, but you are selecting only 7 MB from that table.

If you know this information already, will you apply for a sort-merge join?

No!

I want to use broadcast hash join here because one of my tables is small enough.

Right?

I mean, the table is large, but I am filtering records and selecting only 7 MB of data.

It makes more sense to use broadcast join and avoid the shuffle/sort operation.

Isn't it?

But why isn't it happening?

Well,

Spark will not apply Broadcast hash join if Broadcast Join Threshold is broken.

So you decided to check the following configuration.

But the value is 10MB, and that's the default value.

You haven't changed it.

What does it mean?

Simple!

You are selecting 7 MB from a large table, and that's well below the broadcast threshold.

But Spark is not applying the broadcast join.

Why is that?

Simple!

The Spark execution plan is created before the spark starts the job execution.

Spark doesn't know the size of the table, so it applied a sort-merge join.

Okay, so if I compute statistics on the table, will spark apply the broadcast join?

Well, It may or may not.

Spark will not apply broadcast if you do not have a column histogram for the filter column.

It cannot apply broadcast join if your statistics are outdated.

So one solution is to analyze your Spark tables and keep your table and column statistics up to date.

Another solution is to enable AQE.

Spark Adaptive Query Execution can help you in this situation.

We already learned that the AQE computes statistics on the shuffle data.

Right?

We learned that in the earlier video.

So AQE will compute the statistics on the shuffle data and use that information to do the following things.

We already learned the dynamic coalescing of shuffle partitions in the earlier lecture.

In this lecture, let's see how AQE changes the join strategy.

I enabled AQE and executed the same query to check the new execution plan.

Here it is.

So what is happening here?

We still have three stages.

Stage one and Stage two are scanning the tables and sending data to exchange.

But we enabled AQE.

So the Adaptive Query will compute statistics on the exchange data.

The statistics tell that the data size of large_tbl_2 is small enough to apply broadcast join.

So the AQE will dynamically change the execution plan and apply broadcast hash join.

And you can see that in the new query plan.

Right?

Unfortunately, we still have the shuffle, but we saved the sort operation.

We couldn't save the shuffle operation, and you still see the exchange in the query plan.

Right?

But the sort operation is gone.

AQE cannot avoid shuffle.

Why?

Because the AQE computes the statistics during the shuffle.

Right?

So I will be there.

But the AQE will dynamically change the plan and apply broadcast hash join to save the expensive sort operation.

Make sense?

Great!

But we still have a small problem here.

The shuffle operation is already complete.

We already distributed data from stage one and two to stage three exchange.

Right?

But if we apply broadcast join now, are we going to broadcast the table once again?

Yes.

That's how the broadcast works.

Right?

So AQE also gives you another configuration, as shown here.

The default value for this configuration is true.

So if you are not setting this value false, your optimized execution plan looks like this.

What is the difference?

Both the plans are almost the same.

But you will see this Custom Shuffle Reader.

They also call it the local shuffle reader.

This Custom shuffle reader is specifically designed to further optimize the AQE broadcast join

by reducing the network traffic.

So if you are using AQE, do not disable localShuffleReader.

It is anyway enabled by default.

Make Sense?

Great!

And that's all.

I hope you now understand how AQE dynamically switches the join strategy from a sort-merge join

to a broadcast join.

Let me quickly summarize.

Spark supports many join strategies.

However, the broadcast hash join is usually the most performant.

But you can apply broadcast join if one side of the join can fit well in memory.

And for this reason, Spark plans a broadcast hash join

if the estimated size of a join table is smaller than the broadcast-size threshold.

But estimating the table size is problematic in two scenarios.

You applied a highly selective filter on the table.

or

Your join table is generated at runtime after a series of complex transformations and operations

In the first case, an up-to-date table and column statistics might help.

However, the join table is dynamically created after a series of complex operations in the second case,

so statistics are not even applicable.

Spark AQE replans the join strategy at runtime

based on the most accurate join relation size to solve this problem.

So AQE calculates the table statistics at runtime using the shuffle exchange.

And reoptimizes the query plan converting sort-merge join to a broadcast hash join.

Make sense?

That's all for this video.

See you again in the following video.

Keep Learning and Keep growing.