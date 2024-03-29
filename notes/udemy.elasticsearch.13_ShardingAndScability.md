---
id: a4lwny5s16zjj3jwmstcy6q
title: 13_ShardingAndScability
desc: ''
updated: 1701085906279
created: 1701085688439
---
A bit earlier you learned how a cluster consists of one or more nodes.

This is one of the ways in which Elasticsearch can scale, both in regards to data storage

and disk space.

If we want to store 1 terabyte of data and we only have a single node with 500 gigabytes

of space, that's obviously not going to work.

However, if we add an additional node with sufficient capacity, Elasticsearch can then

store data on both nodes, meaning that the cluster now has enough storage capacity.

But how does that actually work?

Elasticsearch does this by using something called sharding.

You might have heard of this term in the context of databases, for instance, and the concept

is the same, so let's take a look.

Sharding is a way to divide an index into separate pieces, where each piece is called

a shard.

Notice how sharding is done at the index level, and not at the cluster or node level.

That's because one index might contain a billion documents, while another may only

contain a few hundred.

As you might imagine, this affects how the indices are configured.

The main reason for dividing an index into multiple shards, is to be able to horizontally

scale the data volume.

Let me give you a simple example of how sharding works.

Suppose that we have two nodes each having 500 gigabytes of storage space available for

Elasticsearch.

We have a huge index taking up 600 gigabytes of storage on its own, meaning that the whole

index does not fit on either node.

Therefore, running the index on a single shard is not an option, because a shard needs to

be placed on a single node.

Instead, we can divide the index into two shards, each requiring 300 gigabytes worth

of disk space.

Doing this, we can now store a shard on each of the two nodes without running out of disk space.

We could also have a higher number of shards if we wanted to, such as four shards of 150

gigabytes each.

We still have space to spare, so we could use that for other indices if we needed to.

Just to be clear, a shard may be placed on any node, so if an index has five shards,

for instance, we don't need to spread these out on five different nodes.

We could, but we don't have to.

Alright, hopefully you understand how sharding makes it possible to scale the amount of documents

we can store.

Of course sharding goes hand in hand with increasing the available disk space if necessary,

potentially by adding more nodes to the cluster.

Each shard is independent, and you can think of a shard as being a fully functional index

on its own.

That's not 100% accurate, but close enough.

Remember how Elasticsearch is built on top of Apache Lucene?

Each shard is actually a Lucene index.

This means that an Elasticsearch index with five shards, actually consists of five Lucene

indices under the hood.

While a shard does not have a predefined size in terms of allocated disk space, there is

a limit to the number of documents a shard can store, being just over two billion documents.

As I said a few moments ago, the main reason for sharding an index, is to be able to scale

its data volume, being the number of documents it can store.

By using sharding, you can store billions of documents within a single index, which

would usually not be feasible without sharding.

Another common use case for sharding an index, is to divide an index into smaller chunks

that are easier to fit onto nodes.

Yet another reason why sharding is useful, is that it enables queries to be distributed

and parallelized across an index' shards.

What this means, is that a search query can be run on multiple shards at the same time,

increasing the performance and throughput.

Remember that the shards may be stored on different nodes, meaning that the hardware

of multiple nodes may be utilized.

If you are wondering how Elasticsearch searches for data across multiple shards, then don't

worry about that for now, as I will get back to that later in the course when relevant.

Let's head over to Kibana, where I have prepared a query, which you have already seen

before.

Let's go ahead and run it to list the indices within our cluster.

Looking at the results, we can see a column named "pri," being short for "primary shards."

I haven't told you what a primary shard is yet, but I will in the next lecture.

Until then, just think that this column represents the number of shards that a given index has.

We can see that each of our indices are stored on a single shard, being the default setting,

but this can be configured when creating indices.

Up until Elasticsearch version 7, the default was actually for an index to have five shards.

However, having five shards for very small indices is very unnecessary.

When people created lots of small indices within small clusters, they ran into an issue

of over-sharding, meaning that they had way too many shards.

At the same time, it was previously not possible to change the number of shards once an index

had been created.

So what you had to do if you needed to increase the number of shards, was to create a new

index with more shards, and move over the documents.

This was not only inconvenient, but could also be a time consuming process.

To overcome these challenges, indices are created with a single shard by default, since

Elasticsearch version 7.

For small to medium sized indices, this is likely to be sufficient.

If you do need to increase the number of shards, there is a Split API for this.

It still involves creating a new index, but the process is much easier, as Elasticsearch

handles the heavy lifting for us.

To go the other way, i.e. to reduce the number of shards, there is a Shrink API that does

this for us.

These are both topics for later in the course, but for now I just want you to know that the

default number of shards for an index is one, and that this can be changed if need be.

That being said, you should try to anticipate how many documents an index will contain in

the not too distant future when creating an index.

If you think that you will store millions of documents within an index, you might want

to consider adding a few shards at index creation.

Adding the additional shards at the beginning is just easier for you, and you are less likely

to run into bottlenecks down the road.

So how many shards should you choose, then?

This is a question I get asked a lot.

Unfortunately there is no correct answer to this question, other than "it depends."

I know, that's probably not the answer you were hoping for... There is no definitive

answer to this, because it depends on a number of factors, such as the number of nodes within

the cluster, the capacity of the nodes, the number of indices and their sizes, the number

of queries run against the indices, etc.

Needless to say, there are a lot of variables involved, so there is really no formula that

you can use.

However, as a rule of thumb, a decent place to start, would be to choose five shards if

you anticipate millions of documents to be added to an index.

Otherwise, you should be completely fine sticking to the default of one shard and then increase

the number if need be.

To recap, sharding is a way to sub-divide an index into smaller pieces, each being a shard.

This serves two main purposes; enabling the index to grow in size, and to improve the

throughput of the index.

The main reason is to scale data storage, so the increased throughput is probably more

of a bonus.

By using sharding, you can store an index taking up 700 gigabytes of disk space, even

if you have no single node that can store that amount of data.

An index defaults to having one shard.

For small to medium indices, that's usually enough.

For indices that you anticipate will store lots of data, you might want to increase the

number of shards at index creation.

A decent number for most of these cases would be five, but it depends on a number of factors.

If in doubt, I recommend that you just go with the default values and take it from there.