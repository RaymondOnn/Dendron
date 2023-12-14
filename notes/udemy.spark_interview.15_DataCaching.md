---
id: u3bu6l66pm6vhersn2fazfw
title: 15_DataCaching
desc: ''
updated: 1700315383512
created: 1700315114622
---
Welcome Back.

In this video, I will talk about caching Spark data frames in the memory.

We already learned about the Spark memory pool in an earlier video.

Spark memory pool is broken down into two sub-pools.

Storage memory

and Executor memory

We also learned the purpose of these two memory pools.

Right?

Spark will use the Executor pool to perform dataframe computations.

And we can use the storage pool for caching data frames.

But how do you cache your Dataframes in the storage pool?

What are the benefits of caching your Dataframes?

When should we cache, and when should we avoid caching your data frames?

How to uncache it?

What are the different formats of caching?

What if we do not have enough memory to cache the entire dataframe?

Answers to all these questions are essential and critical for any Spark developer.

And that's what we are going to explore and learn in this video.

So let's start with the following question.

How do we cache a DataFrame in the executor storage pool?

Well, we have two methods for doing this.

cache() method

and persist() method

I will show you how to use them in a minute.

But before that, I wanted to talk about the difference between these two.

So the question is this.

What is the difference between cache() and persist()

Both of these methods will cache your dataframe in the executor storage pool.

At a high level, they are the same because they do the same thing - cache the dataframe.

But there is a slight difference.

The cache() method does not take any argument.

However, the persist() method takes an optional argument for the storage level.

So the signature for the persist method is like this.

You also have an alternative like this.

Both of these alternatives are to specify the storage level.

So let me quickly recap the difference between cache() and persist()

The cache method does not take any argument,

and it will cache your dataframe using the default MEMORY_AND_DISK storage level.

However, they persist() method is more flexible.

The persist() method will also cache the dataframe, but it allows you to configure the storage level.

Make sense?

Great!

But what is the storage level?

Let's try to understand it with an example.

Here is a simple Spark DataFrame code.

What am I doing?

I am creating a dataframe of one million rows and repartitioning it to 10 uniform partitions.

Why am I repartitioning?

Because I wanted to demo you how a partitioned dataframe is cached in your spark memory.

Simple.

No other specific reason.

If you are loading a dataframe from parquet or other types of files,

your dataframe is most likely to have multiple partitions.

But I am creating a dataframe at runtime,

so I have to repartition it to show you how multiple partitions behave when you cache it.

Great!

Then I am calling the cache() method to cache my dataframe in Spark storage memory.

Finally, I am executing the take(10) action.

The cache() method is a lazy transformation.

So Spark will not do anything until we execute an action.

That is why I am executing the take(10) action.

The take(10) action will load one partition to Spark memory and give me ten records.

Make sense?

Great!

I ran this code and checked the storage tab of my Spark UI.

Here is the screenshot.

What do you see?

I have one partition in the cache.

This row shows the details of my cached partition.

So what do you learn here?

Many things.

Let me explain it one by one.

I created a dataframe of 10 partitions.

Then I cached it.

But the spark is showing only one partition in the cache.

Why?

Simple.

I used take(10) action.

The take(10) action will bring only one partition in the memory and return one record from the loaded partition.

Right?

That's how take(10) is supposed to work.

Since we brought only one partition to memory, the spark will cache only one partition.

Make sense?

We also learned that Spark would cache the whole partition.

I am taking only ten records from the loaded partition, but Spark will cache the entire partition.

What does it mean?

Simple!

Spark will always cache the entire partition.

If you have a memory to cache 7.5 partitions,

Spark will cache only seven partitions because the 8rth partition does not fit in the memory.

Spark will never cache a portion of the partition.

It will either cache the entire partition or nothing but never cache a portion of the partition.

Make sense?

Great!

We also learned that the default storage level for the cache() method is Memory Deserialized 1 X replicated.

I will come back to the storage level in a minute.

You can also see the size of the cached partition here.

Right?

Now let me change the code and try it again.

I changed the take(10) method and replaced it with the count() action.

The count() action will bring all the partitions in the memory, compute the count and return it.

Right?

I ran this code and check the storage tab once again.

Here is the screenshot.

Great!

Now you see all the ten partitions are cached().

Right?

Why?

Because count() action forced all the partitions to be loaded in the executor memory.

We have enough memory to cache all ten partitions, so Spark is caching all the ten partitions.

Make sense?

The storage level of all the cached blocks is still the same as Memory Deserialized 1 X replicated.

Make sense?

Great!

Now let's use the persist() method and change the storage level.

But before that, let me summarize few things.

Spark cache() as well as persist() methods are lazy transformations.

So they do not cache() anything until an action is executed.

The cache() and persist() methods are smart enough to cache only what you access.

So if you are accessing only 3 or 5 partitions, Spark will cache only those partitions.

Spark will never cache a portion of a partition.

It will always cache the whole partition if and only if the partition fits in the storage.

Make sense?

Great!

Now let me change the code and use persist() method.

Here is my new code.

Everything is the same as earlier.

But now, I am using persist method instead of the cache() method.

And here is the screenshot of Spark UI.

Do you see any difference?

I don't see any.

Why?

Because the cache() method and the persist() method work in the same way.

The persist() method allows us to customize the storage level, but the rest is the same as the cache() method.

In this example, I configured the storage level to the same as cache() storage level.

So you don't see any difference.

But now, let me change the storage level and try to understand the storage characteristics.

Here is the format for the StorageLevel method.

In the current example, I am setting the following values.

So what am I saying here?

I am saying use memory to cache the Spark DataFrame partitions.

Right?

I can also make it like this.

So now I am extending the cache to the disk.

If the partition does not fit into the Spark memory, Spark will cache it in the local disk of the executor.

I can also extend it to off-heap memory for caching my dataframe partitions.

However, we already learned that you should have added some off-heap memory to Spark.

Right?

If you haven't added off-heap memory for your Spark application,

setting this parameter should not have any effect.

Now let's come to deserialization.

What is this?

Let me explain.

Spark always stores data on a disk in a serialized format.

But when data comes to Spark memory, it must be deserialized into Java objects.

That's mandatory for Spark to work correctly.

But when you cache data in memory, you can cache it in serialized format or deserialized format.

The deserialized format takes a little extra space, and the serialized format is compact.

So you can save some memory using the serialized format.

But when Spark wants to use that data, it must deserialize it.

So you will spend some extra CPU overhead.

The point is straight.

You can cache your data in a serialized format

and save some memory but incur CPU costs when Spark access the cached data.

Alternatively, you can cache it deserialized and save your CPU overhead.

The choice is yours, but the recommendation is to keep it deserialized and save your CPU.

And this configuration only applies to memory.

Your disk-based cache is always serialized.

Make sense?

Great!

So let me change the code and set some different storage levels, and try it again.

Here is my new code example.

This time, I am setting the storage level to cache data in memory.

If I do not have enough memory, then cache it in the disk.

I don't want to cache anything in off-heap memory because I haven't added off-heap memory for my application.

I am also willing to try to cache my data in serialized format.

So I am setting the deserialized to false.

The last one is the replication factor.

I am setting it to one because I do not want to cache multiple copies of my data.

If you set this value to 3, Spark will cache three copies of your dataframe partitions.

All those three copies will be cached on three different executors,

but I think caching multiple copies is wastage of memory.

You can cache multiple copies if you have some specific reason.

Otherwise, one copy of cache is more than enough.

Let's try running it and see what happens.

Here it is.

I still see everything cached in memory.

And that's obvious because I have enough memory to cache all my partitions in the memory.

I enabled disk caching also, but Spark will cache it on the disk when I do not have enough memory.

So what I see here is perfectly fine.

This time I stored it as serialized.

So you can see the size of each partition is smaller than earlier.

When I stored it as deserialized, it took more memory space, but the serialized storage takes a little lesser.

Make sense?

Great!

Let me quickly summarize.

The persist method allows you to customize your cache storage level and cache it.

You can cache your data in memory only, or you can cache it in the disk only.

You can also configure it to store both in memory and disk.

If you have some off-heap memory, you can also expand your cache memory to off-heap storage.

So assume you enabled all three storage levels, then Spark will cache your data in memory.

That's the first preference.

If you need more storage, Spark will use off-heap memory.

You still want to cache more data; Spark will use the local disk.

Make sense?

Great!

You can keep your cache in serialized or in deserialized formats.

But this configuration is only applicable for memory.

Disk and off-heap do not support deserialized formats.

They always store data in serialized formats.

The last option is to increase the replication factor of your cached blocks.

Increasing replication can give better data locality to Spark task schedular and make your application run faster.

However, it will cost you a lot of memory.

Make sense?

I used StorageLevel() function to demo the persist method.

But you can also use some predefined constants.

Here is an example.

In this example, I am using a predefined storage level constant.

The constant makes my code more readable, but you can also use the StorageLevel() function.

Here is an indicative list of available constants.

You can refer to the documentation for a more exhaustive list.

The numbers 2 and 3 in these constants represent the replication factor.

Great!

That's almost all except two questions.

How to uncache?

When should we cache, and when should we avoid caching your data frames?

You can remove your date from the cache that you previously cached using the unpersist() method.

We do not have any method called uncache() but unpersist() will do the job.

Now the last question.

When should we cache, and when should we avoid caching your data frames?

When you want to access large DataFrame multiple times across Spark actions,

You should consider caching your data frames.

But make sure you know your memory requirements and configure your memory accordingly.

Do not cache your data frames when significant portions of them don't fit in the memory.

If you are not resuing your data frames frequently or too small, do not cache them.

Make sense?

Great!

That's all for this video.

See you again

Keep Learning and Keep Growing.