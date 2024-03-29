---
id: d20kbedifkxqz0rijcohb9n
title: 15_AddingNodesToCluster
desc: ''
updated: 1701085983297
created: 1701085731931
---
We just talked about sharding and replication.

Sharding enables Elasticsearch to scale the number of documents an index can store by

splitting it into smaller pieces.

Naturally this is not going to help us forever if we don’t add more nodes to our cluster,

as we would otherwise run out of disk space no matter how many shards we create.

Likewise, replication only kicks in for clusters consisting of more than one node.

So instead of just talking about it, let’s actually start up another node and see how

our cluster behaves.

In fact, just for the sake of it, we’ll be spinning up two more nodes.

This lecture is optional, so you are welcome to skip it if it doesn’t interest you.

If you created a cloud deployment instead of a local one, adding additional nodes is

handled within the cloud interface.

This lecture therefore covers how to do this on a local setup, but I do encourage you to

stick around even if you are using a cloud based deployment.

Anyway, let’s begin by looking at the current state of our cluster with the Cluster Health API.

Unsurprisingly, our cluster consists of a single node.

The status is “yellow” because the pages index that we created in the previous lecture

has one unassigned replica shard.

We can see that if we run the second query.

The system indices will have one replica shard created automatically when we add an additional node,

so these indices are not the reason for the “yellow” status.

Don’t worry if you don’t see exactly the same indices, because these system indices

change quite frequently between releases.

Alright, time to start another node and add it to our single node cluster.

First, make sure that you have the archive of Elasticsearch from when we set things up earlier.

Then extract it for each additional node that you want to add to your cluster.

That’s twice in my case, because I want to add two more nodes.

It’s important that you do not make a copy of your existing node’s directory,

because it contains data used by that node.

So be sure to extract the archive to get a clean start for each additional node.

I have done this in advance and renamed the directories to something meaningful.

Before starting up the two nodes, I want to quickly configure their names to make them

easier to tell apart when inspecting the cluster.

To do that, let’s open up a file named elasticsearch.yml, located within the "config" directory.

Near the top of the file, we can find a setting called "node.name," which is commented out by default.

Let’s give it a meaningful value.

That’s all, so I will just save the file and do the same for the other node.

That’s it.

Let’s spin up some nodes.

To add a node, we need an enrollment token, exactly like when we set up Kibana.

Note that this is only needed when starting up a new node for the first time; once it

has joined the cluster, we can start it up as normal without specifying a token.

With the enrollment token, Elasticsearch takes care of a bunch of things for us, meaning

that we don’t have to configure things within configuration files.

The Kibana enrollment token was generated for us automatically, but this time we need

to run a script.

To do that, open up a terminal with the working directory set to the running node’s root directory.

The script we need is named "elasticsearch-create-enrollment-token" and is located within the "bin" directory.

To tell the script which kind of enrollment token we want to generate, we need to provide a scope.

We will set it to "node" this time.

If we wanted to generate a new Kibana enrollment token, we would simply set it to "kibana."

That’s it — let’s run the command.

Make sure that your existing node is running or the command will fail.

Alright, we now have an enrollment token.

Let’s copy it and start up a second node.

I have a terminal opened up with the working directory set to the second node’s root directory.

All we need to do is to run the elasticsearch script within the bin directory, only this

time we need to provide an enrollment token.

Let’s type that out.

Alright, let’s run the command.

The node will take a moment or two to start up, so I will just do a bit of time traveling.

Alright, I’m back!

Our second node has now been started up and has been added to our cluster.

If you check the terminal in which the first node is running, you should see some output

that proves this.

I’m just going to go back to Kibana and inspect our cluster so you can see that everything worked.

If we check our cluster’s health now, we can see that the cluster now consists of two

nodes, and that the health has transitioned to “green.”

That’s because the replica shard for the "pages" index has now been assigned to our second node.

This effectively means that we can lose one of our nodes without losing any data.

Let’s check how the shards have been distributed to verify this.

Indeed we can see that the replica shard has been assigned to our second node.

We can also see that the system indices now have a replica shard each.

That’s because they were all configured with the "auto_expand_replicas setting," meaning

that a replica shard is added when an additional node is added to the cluster.

Also notice how the primary and replica shards are stored on different nodes, exactly as

we talked about in the previous lecture.

That’s because a primary and replica shard are never stored on the same node.

If that were the case, we could lose an entire replication group if a node had a hardware

failure, for instance.

Alright, so that’s how to add additional nodes to your cluster.

Note that this approach is only meant for development purposes;

setting up a multi-node cluster in a production environment requires some configuration.

I am going to start up a third node using the same approach.

If you are following along, I do want to mention something first.

If your cluster consists of three nodes, you are are required to run at least two of them moving forward.

In other words, you won’t be able to run your cluster using a single node.

Without getting into details, this has to do with how Elasticsearch elects a so-called

master node, which it cannot do for a three-node cluster where only one node is available.

The point is that if you want to just run a single node while you work your way through

this course, don’t start up a third node.

Alternatively you can of course try it out and just set up a fresh cluster afterwards.

Anyway, I just wanted to mention that so you are not caught by surprise.

Alright, let’s create another enrollment token, this time for a third node.

Let’s copy it and start up the node.

Alright, I’ll see you when the node has started up.

There we go, the node is now ready.

Let’s inspect the cluster once again.

In particular, I want to take a look at our shards.

Notice how our shards have now been distributed across our three nodes automatically.

Even though our indices fit perfectly well on two nodes, Elasticsearch automatically

distributes them evenly across the available nodes.

That’s just a primitive way of scaling things, because we don’t want our third node to sit idle.

As you can see, Elasticsearch takes care of a lot of things for us automatically,

making our jobs much easier.

Pretty cool, right?

Let’s see what happens if we kill one of our nodes.

Hitting the CTRL + C [control and C] keys simultaneously gracefully shuts down the node.

That will do just fine, but let’s simulate that something went wrong and forcefully shut

down the terminal tab in which the node is running.

This will kill the process abruptly, but the result is really the same.

Alright, our second node is now gone.

Let’s head over to the terminal where our first node is running.

The reason I am looking here is that this is our master node.

I will get back to what this means in the next lecture.

Within the output we can see that a node left the cluster because the connection to it was lost.

We can also see some output saying that delayed shards have been scheduled to be rerouted

in about 60 seconds.

Let’s head over to Kibana to inspect our shards and I will explain what this is all about.

Notice how the shards that were stored on the leaving node are unassigned.

These shards have been marked to be moved to different nodes.

This happens after about a minute.

The reason for that delay is that this can be an expensive maneuver depending on the

sizes of the shards, and so we don’t want it to happen immediately in the case of a

brief networking failure, for instance.

I won’t get into the details of what happens when a node leaves the cluster, but I have

attached a link to the documentation in case you want to do some reading on the subject.

Anyway, I will just head back to the terminal and wait for the delayed shard reallocation to happen.

We can now see that the cluster’s health has transitioned from “yellow” to “green.”

Let’s take a look at the shards once again.

The unassigned shards have now been reallocated to other nodes, and the shards have been balanced

between the available nodes.

This means that our cluster is now fully functional and still protected from data loss in the

case where another node is lost.

If you are following along with me, you can try to start the node that we shut down back up.

You should then see that the shards are balanced across the three nodes once again, just as

if the node never left the cluster in the first place.

Anyway, that’s how to add additional nodes to your development cluster and how Elasticsearch

handles nodes joining and leaving the cluster.