---
id: z99i2xao0vbeyl46cjg38pq
title: 14_DynamicPartitionPruning
desc: ''
updated: 1700315351045
created: 1700315047242
---
Welcome Back.

In this video, I will talk about Dynamic Partition Pruning in Apache Spark.

Dynamic Partition Pruning is a new feature available in Spark 3.0 and above.

And this feature is enabled by default.

However, if you want to disable it, you can use the following configuration.

The default value for this configuration is true in Spark 3.0.

So you don't have to do anything to enable Spark Dynamic Partition Pruning.

But in this video, we want to understand the following.

Why do we need Dynamic Partition Pruning?

and What is Dynamic Partition Pruning?

So let's start with the problem statement and try to understand why do we need it?

Let's assume you have two tables.

The first table is an orders table, and it stores all your orders.

The high-level table structure looks like this.

This table is huge because it stores thousands of orders every day and millions of orders a year.

So you decided to partition it on order_date.

Here is a screenshot of how my orders data set is stored.

I have done it on a local machine.

However, the arrangement is almost the same as on a distributed storage such as HDFS.

But you might ask the following question.

Why do we partition it on the order date?

The answer is simple.

You know you are querying this table on the order date.

Here is an example code.

So what am I doing?

I am reading the orders data set.

Then applying a filter on order_date and finally computing total sales for 3rd Feb 2021.

I ran this code and checked my query plan.

Here is the screenshot.

The right-side image shows the physical plan for this query.

It clearly shows that Spark performed six steps to complete this query.

The first step is to scan or read the parquet file.

Right?

But Spark is doing a smart thing here.

Let's look at the details of step one.

Do you see these Partition Filters?

Spark applied Partition Filters on the order_date column, and it is reading only one partition.

The plan also shows a number of files read one.

Right?

So what is happening here?

In a typical case, Spark should read all the partitions of my parquet data set and then apply the filter.

But my data set was partitioned on the order_date column.

So Spark decided to read only one partition that belongs to 3rd Feb 2021.

Make sense?

Great!

So I am trying to show you two features of Spark query optimization.

Predicate Pushdown

and Partition Pruning

The predicate pushdown means Spark will push down the where clause filters down in the steps

and apply them as early as possible.

I mean, Spark will not try a typical sequence to read the data first, then filter, and finally, calculate the sums.

No!

Spark will push the filter condition down to the scan step and apply the where clause when reading the data itself.

Make sense?

Great!

But predicate pushdown doesn't help much unless your data is partitioned on the filter columns.

My data set was already partitioned on order_date.

Right?

So Spark decided to read only one partition for 3rd Feb 2021.

It can simply leave all other partitions, and that feature is known as Partition Pruning.

Make sense?

Great!

So I hope you now understand predicate pushdown and partition pruning.

These two features will optimize your query and reduce the read volume of your data.

If you are reading less amount of data, your queries run faster.

Right?

Great!

Now come to the dynamic partition pruning.

What is Dynamic Partition Pruning?

Let's try to understand that.

Let's assume you have another table.

Here it is.

Let's call it the dates table.

If you have any experience in a data warehouse, you can quickly identify this table.

This table is known as the date dimension in the data warehousing world.

My orders table is a fact table, and the dates table is a dimension table.

This kind of table structure is common in data warehouses.

Now let's assume I am running a query like this.

So what am I doing here?

I want to calculate the sum of sales for February 2021.

Here is an equivalent dataframe expression.

The SQL and the dataframe expressions are the same.

You can execute this code on an older version of Spark where dynamic partition pruning is not available.

Or, you can disable the dynamic partition pruning feature and try this SQL.

I tried, and I got the following execution plan.

This is a typical sort/merge join execution plan.

I am reading the parquet files and also reading the other data set.

Both are going for a shuffle operation, and hence you can see these two exchanges.

Finally, Spark will sort, then merge and finally aggregate it.

That's all.

My Parquet files are partitioned on the order date column, but that partitioning does not benefit me.

If you see the scan parquet step in the diagram, you can see the number of files read four.

The physical plan shows the partition filter step, but it is not pruning any partitions

because we do not have any filter condition on the order date.

If you look at the SQL query, we are filtering for year and month columns on the dates table.

What does that mean?

Simple!

We want to read all the partitions for February 2021.

But Spark is reading other month partitions also.

Right?

The best case is to read only February 2021 partitions and leave all other partitions.

However, Spark is not applying partition pruning here.

Why?

Because the filter conditions are on the dates table.

They are not on the orders tables.

How to improve it?

You must do two things.

Enable Dynamic Partition Pruning feature.

and Apply broadcast on the dimension table

The Dynamic partition pruning is enabled by default in Spark 3 and above.

So I do not need to do anything because I am using Spark 3.

But I must apply a broadcast to my dimension table.

So I modified my code, and it now looks like this.

This code is the same as earlier.

But now I have applied broadcast() to the date_df.

I ran this code once again, and here is the new execution plan.

Let's look at the plan and see what is happening here.

My dates df is going for a broadcast exchange.

But then, Spark creates a subquery from the broadcasted dates_df and sending it as an input to the parquet scan.

And you can see the number of files read.

We are now reading only two files.

So what happened here?

Spark applied partition pruning, and now it is reading only February 2021 partitions.

If you look at the physical plan and check out the scan parquet details,

you will see a dynamic pruning expression.

The point is straight.

Spark Dynamic partition pruning can take a filter condition from your dimension table

and inject it into your fact table as a subquery.

Once a subquery is injected into your fact table, Spark can apply partition pruning on your fact table.

It works like magic.

But using this feature is not straightforward.

You must understand the following things.

You must have a fact and dimension-like setup.

When I say fact and dimensions, I mean one large table and another small table.

Your large table or the fact table must be partitioned so Spark can try partition pruning.

You must broadcast your smaller table or the dimension table.

If it is smaller than 10 MB, then Spark should automatically broadcast it.

However, you should make sure that your dimension table is broadcasted.

If you meet all three conditions, Spark will most likely apply dynamic partition pruning to optimize your queries.

Make sense?

Great!

That's all for this video.

See you again.

Keep Learning and Keep Growing.