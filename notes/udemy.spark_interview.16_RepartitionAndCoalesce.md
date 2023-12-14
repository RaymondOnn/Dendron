---
id: 1zd2s823bj0zf4021wivxqm
title: 16_RepartitionAndCoalesce
desc: ''
updated: 1700315407778
created: 1700315134495
---
Welcome Back.

I already talked about some critical and applied concepts

such as Spark memory management, AQE, and Dynamic Partition Pruning.

In the next couple of videos, I want to cover the following essential concepts.

These are small and simple knowledge items that are often ignored.

However, I want to cover them and make you familiar with these ideas quickly.

So let's start with the Dataframe repartition, and then I will talk about the Coalesce.

Spark Dataframe API allows you to repartition your Dataframe.

They offer you two methods as listed here.

Both the methods are almost the same except for one difference.

The repartition() method will use a hash function to determine the target partition of your row.

However, the repartitionByRange() will partition your data using a range of values.

For example, 0 to 10 goes in the first partition, then 10 to 20 goes in the second partition, and so on.

That's the only difference.

Otherwise, both the functions work in the same way.

You should also understand one more important thing about repartitionByRange().

The repartitionByRange() method internally uses data sampling to estimate the partition ranges.

So the output of repartitionByRange() may not be consistent.

I mean, if you execute the same code twice, you may see different partition ranges.

And that should be perfectly fine in many cases.

Great!

Now let's see how do they work.

The repartition() method takes two optional arguments.

Number of partitions

and List of columns

One of these two arguments is mandatory.

So all the following calls are valid.

Similarly, you can also assume repartitionByRange() because both have the same structure.

Now let's see how they work.

The repartition(10) will create ten new partitions of your dataframe.

How?

Simple!

The repartition() is a wide dependency transformation.

So it will cause a shuffle/sort of your dataframe, and the final output will have ten new partitions.

And all those ten partitions will be uniform in size.

Here is an example code and a screenshot of Spark UI to explain it.

So what am I doing here?

I am creating a dataframe of one million rows, repartition it to 10 uniform partitions, and cache it.

Why cache it?

Because I want to see them in Spark UI and learn how repartition behaves.

Make Sense?

Great!

So what do you see on the Spark UI?

We have ten partitions, and all are of the same size.

Correct?

So the repartition(10) creates ten uniform partitions.

Now let's create another example and see how repartition works for a column.

Here it is.

What am I doing in this new code?

I am reading a parquet data source and repartitioning it using order_date.

And how many partitions do I get?

I got ten partitions once again.

But How?

Why I got ten partitions, and why not five partitions or maybe 200 partitions?

Let me explain.

The repartition() is a wide dependency transformation.

So it will cause shuffle/sort and repartition of your data using the order_date column.

But the number of partitions is controlled by the spark.sql.shuffle.partitions configuration.

I configured the spark.sql.shuffle.partitions to 10, so I got ten partitions.

Make sense?

But you can override this behavior by supplying the number of partitions.

Here is another example.

This time, I want only five output partitions, and I also want those partitions based on a column name.

So Spark will partition my data on the order_date and limit the number of output partitions to five.

Make sense?

Great!

But you can also see that the partition size is not uniform.

So repartition on a column name does not guarantee a uniform partitioning.

Make sense?

Great!

So let me summarize few points.

You can use repartition() to create uniform partitions of your dataframe.

You can also use one or more columns to repartition your data frames.

Repartition causes a shuffle/sort, and the number of output partitions depends on the shuffle partitions configuration.

You can override the shuffle partition configuration by setting the numPartitions argument.

Repartitioning on column name does not ensure the uniform size of output partitions.

That's all about repartition except for one last question.

When do you want to repartition your dataframe?

Remember one thing.

Repartition will cause a shuffle/sort, which is an expensive operation.

You should almost always try to avoid unnecessary shuffle/sort.

So repartitioning your dataframe must be done with caution.

If you do not see any benefit of repartitioning, you must avoid it.

And I see few situations for repartitioning your dataframe.

You want to reuse the dataframe multiple times and filter your data on some specific column.

In that case, repartitioning on the filter column could be worth it.

You have very few partitions, and your data is not well distributed across your cluster.

In that case, repartitioning with a larger number of partitions

can spread your data more appropriately across the cluster and speed up the rest of your processing.

Your partitions are large, or you have some skewed partitions.

In that scenario, you may want to uniformly repartition your dataframe to a larger number of uniform partitions.

So repartitioning could be helpful in a few scenarios,

but you should make sure that you are getting benefits and possibly test it.

Make sense?

Great!

That's all about repartitioning.

You should use repartitioning when you want to increase the number of partitions or repartition on specific columns.

We should not use repartitioning to reduce the number of partitions.

Instead, you can use the Coalesce method for reducing the number of partitions.

Here is the structure of the coalesce method.

The coalesce() method takes the target number of partitions

and combines the local partitions available on the same worker node to achieve the target.

For example, let's assume you have a ten-node cluster.

And you have a dataframe of 20 partitions.

Those 20 partitions are spread on these ten worker nodes.

Now you want to reduce the number of partitions to 10.

So you executed coalesce(10) on your dataframe.

So Spark will try to collapse the local partitions and reduce your partition count to 10.

The final state might look like this.

You must learn the following things about the coalesce.

Coalesce doesn't cause a shuffle/sort.

It will combine local partitions only.

So you should use coalesce when you want to reduce the number of partitions.

If you try to increase the number of partitions using coalesce, it will do nothing.

You must use repartition() to increasing the number of partitions.

And you should also avoid using repartition to reduce the number of partitions.

Why?

Because you can reduce your partitions using coalesce without doing a shuffle?

Right?

You can also reduce your partition count using repartition(), but it will cost you a shuffle operation.

Make sense?

Finally, coalesce can cause skewed partitions.

So try to avoid drastically decreasing the number of partitions.

It can cause skewed partitions, which may lead to an OOM exception.

Make sense?

Great!

That's all about repartition and coalesce.

See you in the following video.

Keep Learning and Keep growing.