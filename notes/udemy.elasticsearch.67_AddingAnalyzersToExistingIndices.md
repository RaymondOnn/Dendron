---
id: sjugqt96kkb7vz54chbpppm
title: 67_AddingAnalyzersToExistingIndices
desc: ''
updated: 1701193817170
created: 1701105421305
---

In the previous lecture, we added a custom analyzer at index creation time.

That’s often when you will add analyzers, but probably not always.

What if you have already created an index and you want to add an analyzer to it?

That’s what I will show you in this lecture.

While it is fairly straightforward, it is probably not as easy as you think.

Let’s take a look.

We already have the index from the previous lecture which contains one analyzer, so let’s

add another one to that index.

We can do that by using the Update Index Settings API as follows.

This API is not specific to analyzers, just like the “settings” object in the previous query isn’t.

The API can therefore be used for other purposes, such as changing the number of replicas.

The request body format is actually the same as the “settings” object that you saw

before; we just don’t need to add this object because it is implied that we are dealing

with settings when using this API.

That means that we can add the “analysis” object at the root of the request body.

This object uses the exact same format as you have already seen.

To save some time, I will just copy the “analyzer” object from the above query and paste it into

this new query.

I am just going to change the name of the analyzer so a new one will be added.

I will reuse the same configuration because it is not really relevant in this context.

Alright, let’s try to add this new analyzer.

We actually get an error stating that we cannot update non-dynamic settings for open indices.

So what is a non-dynamic setting, and what is an “open” index?

An open index is one that serves requests, and a closed index is… well, the opposite.

Indices can be closed to block read and write requests, meaning that indexing and search

requests are refused.

This is necessary for performing some operations, which leads us to what a “non-dynamic” setting is.

In the context of index settings, there are two kinds of settings; static and dynamic.

Dynamic settings can be changed on an open index, i.e. an index that is actively serving requests.

Static settings, on the other hand, can only be changed at index creation time, or while

an index is closed.

Based on the error message, we can conclude that the analysis settings are static settings,

meaning that we need to close the index before trying to apply the changes.

Fortunately that’s super easy, because we just need to send a POST request to

the Close Index API.

The index is now closed.

Any attempt to search or index documents will be refused while the index is in the closed state.

Having closed the index should allow us to add the analyzer, so let’s run the query again.

Indeed we no longer get any errors.

To enable the index to handle indexing and search requests, we need to open it.

That’s as simple as invoking the Open Index API.

The endpoint is just “_open” instead of “_close,” so we can just copy and modify

the query that we used to close the index.

Now that the index is open, we are free to use the analyzer within field mappings.

You already know how to do that, so I won’t do that now.

Instead, let’s quickly retrieve the index settings and verify that our analyzer is present

and ready for use.

Looking at the results, we can indeed see that two analyzers are now available.

Having to close and open the index is not specific to analyzers, but more precisely

to anything related to analysis.

You therefore need to do the same thing if you want to modify character filters, tokenizers,

or token filters.

That typically goes hand in hand with modifying an analyzer as well, though.

Even though the process of closing and reopening an index is fairly quick, it might not be

an option for some production systems.

It might not be a big deal that the index is briefly unavailable, but for mission critical

systems, it might not be acceptable.

In such a situation, you could alternatively reindex documents into a new index containing

the new configuration and then use an index alias during the transition.

Typically, closing and opening an index is not a problem during maintenance windows, though.

Now that you know how to add new analyzers to existing indices, what about updating analyzers?

Let’s take a look.