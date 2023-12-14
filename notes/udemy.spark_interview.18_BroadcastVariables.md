---
id: h449mgdrqzsx0dexaz89z3p
title: 18_BroadcastVariables
desc: ''
updated: 1700315445141
created: 1700315170849
---
Welcome Back.

In this video, I will talk about Spark Broadcast Variables.

Spark broadcast variables are part of the Spark low-level APIs.

If you are using Spark Dataframe APIs, you are not likely to use broadcast variables.

They were primarily used with the Spark low-level RDD APIs.

However, it is essential to understand the concept.

So let me explain it at a high level.

We will use an example code to explain the concept.

Let me quickly explain the requirement, and then I will explain the code.

I want to create a Spark UDF and use it in my spark project.

As we already know, a Spark UDF is nothing but a function

that takes one or more columns as input and returns one transformed column as output.

So I wanted to create udf of the following structure.

This function takes a string product_code and returns a string product name.

And for translating the product code to a product name, the function references a small lookup table.

Here is the lookup data structure.

I am showing a super simple and small lookup dataset here.

But you can think of it as a 5 MB or 10 MB dataset in a real-life scenario.

So the point is straight.

We want to create a UDF and provide it with two inputs.

The first input is a product_code which goes as a function argument.

The second input is a large dataset that should be available to function as a lookup table.

Make sense?

Great!

This is a general requirement.

In these kinds of requirements, we want to create and use UDF.

But we also want to make available some reference data to the Spark UDF.

We cannot pass this reference data to the UDF as a function argument.

So how do we provide it to the UDF?

One easy way is to keep the lookup data in a file, load it at runtime and share it with the UDF.

But how do we share it with the UDF?

We have two ways to do it.

Closure

and Broadcast

Let me show you the code and explain how you can do it.

Here is a super simple example.

I am creating a Spark session, and you already know all this.

Then I read a lookup.csv file from the storage and brought it to the Spark driver as a python dictionary.

Now the prdCode is a Python dictionary object available at the Spark driver.

And we want to share this prdCode object with my UDF.

So, I will broadcast this prdCode object to the Spark cluster.

Now, bdData is my broadcast variable.

So I have two variables here.

prdCode and bdData

prdCode is a standard Python dictionary variable.

However, the bdData is a broadcast variable.

I want to demonstrate a working example.

So I am creating a dataframe at runtime.

I will use this dataframe to test my UDF.

In the following two lines, I am registering my UDF function as Spark SQL UDF and applying it to the dataframe.

All of this is a super simple and standard method for using a Spark UDF.

Now, let's scroll up to the UDF definition.

This is my super simple UDF.

I am accepting the product code as input and returning the product name from the bdData broadcast variable.

So what is happening here?

My UDF function will be called for each record in my dataframe.

Right?

While calling the UDF, I am passing the product_code column to the UDF.

The UDF will look for the product_code in the lookup and return the corresponding product name.

Simple.

I am using the broadcast variable here.

But You can also use the prdCode directly.

Both the approaches are going to work.

But if you are using prdCode, you are using a lambda closure.

And if you are using bdData, you are using a broadcast variable.

But what is the difference between these two approaches?

If you are using a closure, Spark will serialize the closure to each task.

If you have 1000 tasks running on a 30 node cluster, your closure is serialized 1000 times.

But if you are using a broadcast variable,

Spark will serialize it once per worker node and cache it there for future usage.

So you have 1000 tasks and 30 worker nodes.

Right?

In that case, the broadcast variable is serialized only 30 times, once per worker node.

Make sense?

Great!

So let me summarize it.

Broadcast variables are shared, immutable variables

cached on every machine in the cluster instead of serialized with every single task.

And this serialization is lazy.

What does it mean?

The broadcast variable is serialized to a worker node only

if you have at least one task running on the worker node that needs to access the broadcast variable.

However, you should also remember that the broadcast variable must fit into the memory of an executor.

Make sense?

Great!

One last thing before we close this lecture.

Spark Dataframe APIs use this same technique to implement broadcast joins.

So they will bring a small table to the driver as we did in this example.

Then they will broadcast it to the workers for being used in the join.

So if you carefully design your application logic,

you can meet your requirement using broadcast joins in most cases.

However, if you plan to use UDF

and make some datasets available to your UDF for reference,

you can use the broadcast variables, as I demonstrated in this example.

Make sense?

Great!

See you again.

Keep Learning and Keep Growing.