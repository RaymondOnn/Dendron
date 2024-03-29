---
id: di36glfho3kljy089sf45fj
title: 14_Replication
desc: ''
updated: 1701085949493
created: 1701085711104
---
Now that you know what sharding is all about, let's look at something different, although

related - namely replication.

When we talked about sharding, you learned that an index consists of a single shard by default.

But what happens if the node where a shard is stored breaks down, i.e. has a disk failure?

The answer is simple; the data is lost, since we have no copy of it.

That's obviously a major problem, as hardware can fail at any given time.

The more hard drives that are used to run your cluster, the higher the chances of a failure.

Obviously this needs to be dealt with, because we want to be able to sleep at night, knowing

that a hard drive failure is not a disaster.

Therefore we need some fault tolerance and failover mechanism, which is where replication

comes into the picture.

Elasticsearch natively supports replication of shards, and this is actually enabled by

default, with zero configuration.

How cool is that?

Many databases also have replication functionality, but setting it up can be quite complicated,

so getting all of this for free is really awesome!

So how does replication work in Elasticsearch?

As you know, an index is configured to store its data within a number of shards, which

may be stored across multiple nodes.

Likewise, replication is also configured at the index level.

Replication works by creating copies of each of the shards that an index contains.

These copies are referred to as replicas or replica shards.

A shard that has been replicated one or more times, is referred to as a primary shard.

A primary shard and its replica shards, are referred to as a replication group.

Replica shards are a complete copy of a shard that can serve search requests just like the

primary shard.

When creating an index, we can choose how many replicas of each shard that we want,

with one being the default value.

So an index contains shards, which in turn may contain replica shards.

In the example diagram that you can see on your screen, an index has been split into

two primary shards, each having two replica shards.

The index therefore contains two replication groups.

Great, so we have physical copies of each of our shards, but how does that help if the

entire disk stops working, and we lose all of its data?

To prevent this from happening, replica shards are never stored on the same node as their

primary shard.

This means that if one node disappears, there will always be at least one copy of a shard's

data available on a different node.

How many copies will be left, depends on how many replicas are configured for the index,

and how many nodes the cluster contains.

On the diagram, you can see that the replica shards are placed on a different node than

the primary shard they belong to.

Replication only makes sense for clusters that contain more than one node, because otherwise

replication is not going to help if the only available node breaks down.

For this reason, Elasticsearch will only add replica shards for clusters with multiple nodes.

You can still configure an index to contain one or more replicas for each shard, but it

will not have any effect until an additional node is added.

Let's go through an example of how replication works within an Elasticsearch cluster.

To keep things simple, let's say that we only have two indices within the cluster,

and that both indices use the default configuration.

We start out with a cluster consisting of a single node.

Each index contains only one shard, so the node will contain a total of two shards.

Even though the indices are configured to replicate each shard once, the replica shards

will be unassigned because we only have a single node running.

This is fine for a development environment because it's only inconvenient if we lose data.

However, for a production environment, we really don't want to risk losing any data,

so we decide to add an additional node to the cluster.

Remember that these nodes don't need to be powerful at all; they just need to run

on independent hardware so that there is no single point of failure.

Once Elasticsearch recognizes that we have added an additional node, it will enable replication,

meaning that the replica shards will be assigned.

Had we configured the indices to replicate shards twice, two replica shards would be

placed on the other node instead, but the concept remains the same.

If we would then add an additional node to the cluster, we would see that the replica

shards would be spread out to increase the availability even more.

In that case, we wouldn't lose any data even if two nodes went down at the same time.

Typically you would be fine with one or two replicas, but that depends on how critical

your setup is.

If two nodes break down at the same time, can you then restore the data that was stored

on them from another data source, such as a relational database?

And is it even acceptable for the data to be unavailable while you restore it?

If you are using Elasticsearch for powering the search functionality on your personal

WordPress blog, you would probably be fine with this very small risk.

On the other hand, if you are using Elasticsearch for something critical within a hospital,

you probably cannot afford to take that risk.

As a rule of thumb, you should replicate shards once, and for critical systems, you should

replicate them twice or more.

This also means that for a production setup, you will need at least two nodes to protect

yourself against data loss.

While we are talking about preventing data loss, I want to briefly mention that Elasticsearch

also supports taking snapshots, just like many databases do.

Snapshots provide a way to take backups so that you can restore data to a point in time.

You can either snapshot specific indices, or the entire cluster.

So if we can take snapshots, why do we need replication?

Replication is indeed a way of preventing data loss, but replication only works with

"live data." This essentially means that replication ensures that you won't lose

the data that a given index stores at the current point in time.

Snapshots, on the other hand, enable you to export the current state of the cluster (or

specific indices) to a file.

This file can then be used to restore the state of the cluster or indices to that state.

For instance, imagine that we have been tasked to restructure how millions of documents are

stored within an index.

Of course we think that it's going to work, and we have tested it on our development machine.

To be sure that we can recover from any implications, we snapshot the index before running the queries.

When running the queries, things didn't go as planned, perhaps because our test documents

differed from the documents stored within the live index.

Whatever the cause, the documents got messed up, and we need to revert the changes to get

things back to a working state.

Replication cannot help with that, because replication just ensures that we don't lose

our latest data, which has already been modified in this example.

Instead, we need to revert the state of the index to the snapshot that we took.

Doing that, we should be all good and ready to try again after having fixed whatever went wrong.

So hopefully you understand the difference between snapshots and replication.

Snapshots are commonly used for daily backups and manual snapshots may be taken before applying

changes to data, just to make sure that there is a way to roll back the changes in case

something goes wrong.

Replication just ensures that indices can recover from a node failure and keep serving

requests, as if nothing had happened.

Replication actually serves a secondary purpose as well.

Apart from preventing data loss, it can be used to increase the throughput of a given

index.

Suppose that we have a web shop where we have the products stored within an index named

"products." We display the most popular products on the front page, and we also run

queries against the index when users search for products.

Needless to say, this index gets queried a lot.

The index is configured to only have one shard, because we don't have that many documents;

we just run a lot of queries against the index.

The replica count is also set to just one.

We are starting to experience a performance bottleneck for the queries run against the

index at peak hours, so we need to find a way to handle that.

The initial thought could be to add an additional node to the cluster, which by the way currently

consists of two nodes.

Having only a primary shard and one replica shard, that is not going to help, because

we cannot spread these out across more than two nodes anyway.

For us to utilize the additional node, we would have to increase the number of shards,

or the number of replica shards.

We don't really need another node, and we also don't want to increase our costs.

Instead, we can increase the number of replica shards by one, or however many we need.

Since we only have two nodes, we are not really increasing the availability of the index,

but we are increasing the throughput of the index.

But how?

Remember how I told you that a replica shard is a fully functional index on its own, just

like a shard is?

This actually means that both of the replica shards can be queried at the same time.

This is possible because of two things; the fact that Elasticsearch automatically coordinates

where queries will be executed, and parallelization.

Elasticsearch will automatically select the shard that will execute a given query, based

on a number of factors that we will get back to later in the course.

This means that if we have three search requests coming in at the same time, they can be executed

on three different shards; the primary shard, and both of the replica shards.

So in that case we can have three queries running at the same time, searching the same index.

But how is that possible when we only have two nodes, you might wonder?

That's possible because any CPU that is less than a decade old, has multiple cores

- typically at least four.

This enables the CPU to run tasks simultaneously on each of the cores by using threads.

In this example, this means that the node hosting the two replica shards can run a search

query on each of the shards in parallel, thus increasing the throughput of the index.

Of course adding more replica shards will only be beneficial in terms of performance

if the hardware resources of the node have not yet been fully utilized.

That's the case in our example, since the load is unevenly distributed across our indices.

If the nodes were already busy handling requests for other indices, we would see little to

no effect of adding an additional replica shard.

On top of that, we also need additional disk space to store the replica shard, since it

is a full copy of the primary shard.

As I'm sure you can see by now, there are a lot of variables that affect how many nodes,

shards, and replica shards are optimal.

This was just an example of how replication can be used to increase the throughput of

an index.

So to summarize, replication serves two purposes; increasing availability and throughput of

an index.

Let's head over to Kibana for a moment, because I want to show you something.

First up, let's actually create a new index.

That's super easy; we just specify the "PUT" HTTP verb, followed by the name of the index

we want to create.

I'll just give it a name of "pages."

Great, our index has now been created.

Since we didn't specify any settings, it was created using the default settings, being

one shard and one replica shard.

Let's inspect the cluster again.

For that, I have prepared two queries that you have seen before, so let's begin by

checking the cluster's health.

Notice how the cluster's health has changed from "green" to "yellow," so what's

up with that?

Let's list the indices that our cluster contains for clues.

Here we can see that the status of our newly created index is set to "yellow." The

reason for that, is that the index contains a replica shard, but that shard is not allocated

to any node.

You already know the cause of that, being that a replica shard is never allocated to

the same node as its primary shard.

And since our cluster only contains a single node, Elasticsearch has nowhere to allocate

the replica shard to.

The replica shard is therefore pending to be allocated, which will happen if we add

an additional node.

So that's the reason our cluster and index is in a "yellow" state.

The index is fully functional, but we are at risk of a data loss in case the node goes

down, so the "yellow" status is a warning for that.

Let's now list all of the shards within our cluster, and see where they have been

allocated.

For that, we can use the "_cat" API and its "shards" command.

The results show a list of each shard along with various information about it, including

which index it belongs to.

At the top, we can see two shards for our newly created index, with one being the primary

shard, and one being the replica shard.

This is specified within the "prirep" column, where "p" corresponds to "primary

shard," and "r" corresponds to "replica shard." The next column specifies the state

of the shard.

As we can see, the primary shard has a state of "STARTED," meaning that it is fully

functional and available for requests.

The replica shard, on the other hand, has a state of "UNASSIGNED," which is because

we need to add another node to the cluster for replication to have any effect.

Now that you know the reason our cluster is in a "yellow" state, let's review the

list of indices once again, because I want to show you one last thing.

Did you notice that the Kibana indices are configured with one shard and zero replicas?

The one shard makes sense because these indices will be storing very small amounts of data,

and the query throughput will be limited.

But doesn't having no replica shards put us at risk of losing data?

Yes, indeed it does.

Don't be fooled by these zeroes, though, because if we were to add another node to

the cluster, these zeroes would increase to one.

How?

Because the Kibana indices are configured with a setting named "auto_expand_replicas"

with a value of "0-1." What this setting does, is to dynamically change the number

of replicas depending on how many nodes our cluster contains.

When we only have a single node, there will be zero replicas, and for more than one node,

there will be one replica.

You will see this in action soon, so I just wanted to point it out now in case you were wondering.

That's the end of this lecture.

You should now have a decent understanding of how replication works in Elasticsearch,

and the purpose of having replica shards.