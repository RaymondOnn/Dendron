---
id: twf6uzz742320k50o0j1bun
title: 27_HowESReadsData
desc: ''
updated: 1701104113927
created: 1701080801469
---
We just talked about what routing is.

Let's now go into a bit more detail about how Elasticsearch reads data.

As you know, routing plays a key role in this, but there is more to it than that.

I want to mention that what we will cover in this lecture, is related to reading a single

document, and not how search queries work; we will cover that later in the course when

the time is right.

The very first thing that happens, is that a given node receives the read request.

This node is responsible for coordinating the request, and is therefore referred to

as the coordinating node.

So what does this coordination involve?

The first step is to figure out where the document we are looking for is stored.

As you know, that's done with routing.

When we talked about routing, I told you that routing resolves to the shard that stores

a given document.

That is still true, but more specifically it resolves to a primary shard (or a replication

group).

That's of course assuming that the shard has been replicated, but that is almost always

the case, for reasons that we discussed earlier.

If Elasticsearch just retrieved the document directly from the primary shard, all retrievals

would end up on the same shard, which of course doesn't scale well.

Instead, a shard is chosen from the replication group.

Elasticsearch uses a technique called Adaptive Replica Selection (ARS) for this purpose.

What ARS essentially does, is to select the shard copy that is deemed to be the best.

This is evaluated by a formula that takes a number of factors into account, but I won't

get into that right now.

We will talk about ARS in more detail a bit later in the course.

For now, just know that Elasticsearch tries to select the shard copy that it believes

can yield the best performance.

Once a shard has been selected, the coordinating node sends the read request to that shard.

When the shard responds, the coordinating node collects the response and sends it to

the client.

The client will typically be an application using one of the Elasticsearch SDKs, but it

could also be Kibana or your development machine if you send requests from the command line.

Now that you know how reading data works at a high level, let's move on to talking about

how writing data works.