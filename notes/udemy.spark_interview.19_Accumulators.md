---
id: xercpiczeyz62c4a2lja41q
title: 19_Accumulators
desc: ''
updated: 1700315477679
created: 1700315191737
---
Welcome Back.

In this video, I will talk about Spark Accumulators.

Spark accumulators are part of the Spark low-level APIs.

If you are using Spark Dataframe APIs, you are not likely to use accumulators.

They were primarily used with the Spark low-level RDD APIs.

However, it is essential to understand the concept.

So let me explain it at a high level.

We will use an example code to explain the concept.

Let me quickly explain the requirement, and then I will explain the code.

I have a dataframe that looks like this.

This dataframe represents an aggregated shipment record.

We have a source column, then a destination column, and finally, we have a shipments column.

The shipments column represents the number of shipments from the given source to a destination.

However, we have a slight problem here.

The shipment column is expected to be an integer column.

But we have some bad records in this table.

I want to fix these bad records.

But how do we do it?

I discussed it with the business team, and they suggested a super simple solution.

We are asked to take null if the shipments count is not a valid integer.

So I decided to create a UDF for this.

Here is the code for the UDF.

This UDF simply takes the shipments column, converts it into an integer, and returns it.

If we cannot convert the shipments column to an integer, we return null.

Simple!

Isn't it?

How to use this UDF?

How to use this UDF?

Here is the code.

So I will register the UDF and use it to create a new column with the correct values.

Make sense?

Here is the expected output.

Looks good.

Right?

I replaced the bad values with nulls.

Correct?

But now, I have another requirement.

We also want to count the number of bad records.

How to do it?

Can you think of some solution?

We have an easy way to do it.

I can simply count the nulls in the shipments_int column.

Correct?

We fixed two rows in this dataframe, replacing two bad values with nulls.

So If I count the nulls in the new column, I exactly know the number of bad records.

The solution is perfectly fine.

But count() aggregation or a count() action on a dataframe has a wide dependency.

Spark will add one extra stage and a shuffle operation.

My execution plan looks like this.

So you can see this exchange in my execution plan.

This exchange represents a shuffle that is caused by the count() operation.

And that's not a good thing.

I don't like this shuffle exchange in my plan?

Can I do something else and avoid this shuffle?

I wish I had a global variable to increment it from my UDF while I am fixing the bad record.

And that's precisely what Spark Accumulators are.

Spark Accumulator is a global mutable variable that a Spark cluster can safely update on a per-row basis.

You can use them to implement counters or sums.

Let me show you a complete example.

Here is the code.

I created an accumulator using the spark context with an initial value of zero.

So the bad_rec is an accumulator variable.

Then I use this accumulator variable in my UDF.

Here it is.

So whenever I see a bad record, I will increment the accumulator by one.

That's all.

Super simple.

Right?

Now come back to the end of the program.

This accumulator variable is maintained at the driver.

So I can simply print the final value.

I do not have to collect it from anywhere.

Because the accumulators always live at the driver.

All the tasks will increment the value of this accumulator using internal communication with the driver.

Make sense?

Great!

So I do not have to include an extra stage and a shuffle for calculating the count of bad records.

I am always incrementing it as I discover a bad record.

Make sense?

Let's look at the execution plan.

The first is the old plan with a count(), and the second uses an accumulator variable.

I saved a shuffle and a hash aggregation.

Right?

Great!

So that's one potential use of an accumulator.

You can use accumulators for counting whatever you want to count while processing your data.

They are similar to global counter variables in spark.

But remember a few essential things.

I used the Spark accumulator from inside a withColumn() transformation.

I mean, I used it from inside the UDF, but I am calling the UDF inside the withColumn() transformation.

So effectively, the accumulator is used inside the withColumn() transformation.

Correct?

That's perfectly fine.

But some people use the accumulator from inside an action.

For example, we also have a forEach() method on a PySpark Dataframe.

That's an action.

The forEach() action takes a lambda and applies the lambda function on each row.

You can also use an accumulator from within the forEach() action.

Right?

The point is straight.

You can increment your accumulators from inside a transformation method or from inside an action method.

But it is always recommended to use an accumulator from inside action

and avoid using it from inside a transformation.

Why?

Because Spark gives you a guarantee of accurate results when the accumulator is incremented from inside an action.

We already know, some spark tasks can fail for a variety of reasons.

But the driver will retry those tasks on a different worker assuming success in the retry.

Spark may also trigger a duplicate task if a task is running very slow.

Right?

The point is straight.

Spark runs duplicate tasks in many situations.

So if a task is running 2-3 times on the same data,

it will increment your accumulator multiple times, distorting your counter.

Right?

But if you are incrementing your accumulator from inside an action, Spark guarantees accurate results.

So Spark guarantees that each task's update to the accumulator will be applied only once,

even if the task is restarted.

But if you are incrementing your accumulator from inside a transformation, Spark doesn't give this guarantee.

Make sense?

Great!

That's all about accumulators except two last points.

Spark in Scala also allows you to give your accumulator a name and show them in the Spark UI.

However, PySpark accumulators are always unnamed, and they do not show up in the Spark UI.

Spark allows you to create Long and Float accumulators.

However, you can also create some custom accumulators.

But that part is outside the scope of this course

because accumulators are now used only for a few specific scenarios.

Great!

That's all for this video.

See you again.

Keep Learning and Keep growing.