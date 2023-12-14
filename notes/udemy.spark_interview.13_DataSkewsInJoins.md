---
id: 3xb4kddgl6wxeszo5chx93i
title: 13_DataSkewsInJoins
desc: ''
updated: 1700315323428
created: 1700315006088
---
Welcome Back.

In the earlier videos, I talked about the three features of Spark Adaptive Query Execution.

Here they are.

I also covered the first two.

In this video, I will talk about the Dynamically optimizing skew joins.

So let's start understanding the problem first.

Why do we need dynamic join skew optimization?

Let's assume you have two larger tables.

And you are joining these two tables using the following query.

I am showing a Spark SQL here, but you might be doing it using an equivalent dataframe expression as shown here.

Both are the same.

Both of your tables are large tables, so you are expecting a sort-merge join to take place.

You ran the job and checked the execution plan.

Here it is.

So what is happening here?

I am reading table one and table two.

These two tables should join, so we have a shuffle operation for both tables.

And that's why you see these two exchanges here.

The first exchange partitions the data by the join key for the first table.

And the second exchange partitions the data by the join key for the second table.

Now let's dig deeper into these two changes.

Let me assume that my first table had two partitions.

Here it is.

Each color represents data for one unique join key.

So we read these two initial partitions, and then we shuffled them.

Right?

The result of the shuffle looks like this.

Make sense?

The primary purpose of the shuffle is to repartition the data by the key.

And that's what is happening here.

What about the second table.

That also goes through the same process.

Let me add that to the diagram.

Make sense?

So partitions for the first table are coming from the left side, and the second table comes from the right side.

Spark appropriately partitioned the data by the key.

Now, all we need to do is sort each partition by the key and merge the records from both sides.

I have four partitions to join.

So I will need four tasks.

Each task will pickup one color, sort the data, and merge it to complete the join operation.

Simple! Isn't it.

Do you see any problem?

I do.

The green color partition on the left side is quite large.

Isn't it?

The green partition is skewed.

Look at the blue, orange, and yellow.

They are smaller, but the green one is almost double the size.

And that's a problem.

I need more memory to sort and merge the green partition.

I planned 4 GB RAM for each task, and that should be sufficient for the other tasks.

But the task working with the green color cannot manage the sort/merge operation with 4 GB RAM.

What can I do?

Should I increase the Spark memory?

Well, I can increase it, but that's not a good solution.

Why?

I have two reasons.

The first reason is memory wastage.

I can increase the memory, assuming that I will have a skewed partition.

But I am not sure if all my joins will result in a skewed partition.

What if they don't?

You might have 10 or 15 join operations in your Spark application.

All other join operations work perfectly fine with 4 GB task memory.

However, you have one skewed join, and one task needs extra memory.

Increasing memory for one specific join or task is not possible.

So you will end up increasing memory for your entire application, and that's wastage.

Increasing memory for skewed join is not a permanent solution.

Why?

Because data keeps changing.

Right?

You might have a skewed partition today, which required 6 GB to complete the sort/merge operation.

But you do not know what happens a week later.

You got new data, and the skew is now more significant.

You now need 8 GB to pass through that skew.

I cannot let my application fail every week or month, investigate the logs,

and identify that we need more memory because data is now more skewed.

That's ridiculous.

Right?

So what is the solution?

Spark AQE offers you an excellent solution for this problem.

You can enable skew optimization using the following configurations.

The first configuration enables the Spark AQE feature.

The second configuration enables the skew-join optimization.

We have two more configurations, and I will talk about them in a minute.

But let's try to understand what happens when we enable AQE and skew-join optimization.

So here we are with our scenario.

I have four shuffle partitions, so I will need four tasks to perform this sort/merge join operation.

However, the green partition is skewed.

So the green task will struggle to finish and take longer to complete.

In the worst case, it might fail due to a memory crunch.

But the other three tasks will complete quickly and normally because they are small enough.

However, I enabled AQE and skew-join optimization.

So Spark AQE will detect this skewed partition.

And it will do two things.

Split the skewed partition on the left side into two or more partitions.

In our example, splitting into two is sufficient so let's assume Spark splitting it into two partitions.

Spark will also duplicate the matching right-side partition.

That's all.

Problem solved.

Now we have five partitions.

Earlier, we had four, but now we have five partitions.

So we will need five tasks.

The data partitions for all five tasks are almost the same,

so they will consume uniform resources and finish simultaneously.

Make sense?

Great!

That's how AQE and Skew Join optimization works.

I talked about two configurations.

We also have two more configurations to customize the Skew Join optimization.

These two configurations are used to define the skew.

What does it mean?

Simple!

When do we consider a partition as a skewed partition and start splitting it?

Spark AQE assumes that the partition is skewed and starts splitting when both thresholds are broken.

Now let's talk about these thresholds.

The default value of skewedPartitionFactor is five.

So a partition is considered skewed if its size is larger than five times the median partition size.

The default value of skewedPartitionThresholdInBytes is 256MB.

So a partition is considered skewed if its size in bytes is larger than this threshold.

But remember!

Spark AQE will initiate the split if only if both the thresholds are broken.

Make sense?

Great!

That's all about the Spark AQE Skew join optimization.

I started the Spark AQE discussion with the following three features.

And I covered all three features in three videos, including this one.

That's all about the Spark AQE.

See you again.

Keep Learning and Keep Growing.