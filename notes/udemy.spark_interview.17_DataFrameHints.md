---
id: nm1ltxys2dsi0uq302p3wqe
title: 17_DataFrameHints
desc: ''
updated: 1700315429939
created: 1700315153021
---
Welcome Back.

In this video, I am going to talk about the hints in Spark.

Spark allows you to add two types of hints to your data frames and Spark SQL.

Partitioning Hints

and Join Hints

We have the following four partitioning hints.

The COALESCE hint can be used to reduce the number of partitions to the specified number of partitions.

It takes a partition number as a parameter.

I will show you an example of this hint and help you learn how to use it and how it works.

The REPARTITION hint can be used to repartition your dataframe to the specified number of partitions.

It takes a partition number, column names, or both as parameters.

The REPARTITION_BY_RANGE is similar to REPARTITION, but it uses the data range for partitioning.

We already learned about repartition and coalesce in an earlier video.

These hints also offer similar functionality and behave in the same manner.

But since these are hints, Spark doesn't guarantee that it will apply the hint.

Great!

So we have another REBALANCE hint.

This one is added later than spark 3.0.

The REBALANCE hint can be used to rebalance the query result output partitions

so that every partition is of a reasonable size (not too small and not too big).

It can take column names as parameters and try its best to partition the query result by these columns.

This is the best effort: Spark will split the skewed partitions to make these partitions not too big if there are skews.

This hint is helpful when you need to write the result of this query to a table to avoid too small/big files.

This hint is ignored if AQE is not enabled.

That's all about the repartition hints.

We also have four join hints.

These hints allow you to suggest the join strategy for Spark.

When different join strategy hints are specified on both sides of a join,

Spark prioritizes hints in the same order as I have listed here.

The last one is known as shuffle-and-replicate nested loop join.

These join hints also have some alias.

So the BROADCAST and BROADCASTJOIN and MAPJOIN are the same.

They are just alias for the same hint.

Great!

That's all about listing the join hints.

Now let's see how to use them.

If you are using Spark SQL, you can use these hints using the following syntax.

And here are some examples.

So using join hints in Spark SQL is simple and straight.

But how do we apply them in Dataframes?

We have two methods to apply hints in a dataframe.

Use spark sql functions

or use Dataframe.hint() method

I have an example here that uses both types of hints in a dataframe.

So what am I doing?

I am creating two Dataframes flight_time_df1 and flight_time_df2.

The df1 is a large dataframe, and the df2 is a small one.

So I wanted to apply the broadcast hint to the df2 so I can avoid shuffle sort.

And that's why I applied a broadcast() function around the flight_time_df2.

The broadcast() function is one approach to apply the broadcast hint to a dataframe.

I could have also done it like this.

Both the methods have the same effect.

Finally, I am joining two data frames and applying a coalesce hint to the join result.

I know the default shuffle partition value is set to 200.

So If I do not apply the coalesce hint, the join_df will have 200 partitions.

But I applied a coalesce(5) hint.

So Spark will coalesce it into five partitions only.

Make sense?

Great!

I executed the above code without these two hints and also executed them with both the hints.

Here are two execution plans.

The left side plan is for without the hints.

And the right side plan is for both the hints.

You can see that the first plan applies shuffle sort and then a sort-merge join.

The second plan applies a broadcast join and also applies a coalesce in the end.

Make sense?

Great!

That's all for this video.

See you again.

Keep Learning and Keep Growing.