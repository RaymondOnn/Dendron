---
id: 4p6p4yrxgs9f6qlowsgu6lx
title: 26_UnderstandingRouting
desc: ''
updated: 1701104093640
created: 1701080780612
---
We have now indexed, updated, deleted, and retrieved documents. That was pretty easy,

wasn't it? Perhaps one question comes to mind, though; how does it work under the hood?

Particularly, how did Elasticsearch know on which shard to store the documents? And how

did it know on which shard to find an existing document, be it to retrieve, update, or delete it?

The answer to both those questions is routing. In its basic form, routing refers

to the process of resolving a document's shard, both in the context of retrieving the

document, but also to figure out on which shard it should be stored in the first place.

When we index a new document, Elasticsearch uses a simple formula to calculate on which

shard the document should be stored. You can see the formula on your screen now.

I will get back to what "_routing" is soon, but by default, this equals the document's ID.

The same applies when we update or delete documents.

What happened when we retrieved a document by its ID, was that the same formula was used

based on the ID that we specified. Elasticsearch then knew that if the document existed, it

had to be on the shard that the routing formula yielded. The same applies for updating and

deleting specific documents. Searching for documents based on some criteria other than

their IDs, works differently as you will see when we get to writing our first search query.

As you have seen, routing is entirely transparent to us as users of Elasticsearch. By providing

a default routing strategy, Elasticsearch is much easier to use than if we had to deal

with this ourselves. Did you notice how I said "default" routing strategy? That's

because it is possible to change the default routing. I won't go into detail with this

now, because that's a slightly more complicated subject that we will look at later in the course.

Apart from ensuring that documents are allocated to a shard and that we can retrieve documents

by ID, the default routing strategy comes with another benefit; it ensures that documents

are distributed evenly across an index' shards. If we were to change how documents

are routed, we would either have to ensure that documents are distributed evenly, or

accept that one shard may store significantly more documents than another shard.

Elasticsearch stores a bit of metadata together with the documents that we index. As you saw

when we retrieved a document, the JSON we specified when adding a document, is stored

within an '_source' field.

You also saw that a document's identifier is stored within

an "_id" field. Another meta field, is the one named "_routing," which you saw

in the routing formula a moment ago. Since the default routing strategy uses the document's

ID, you won't see this "_routing" field as part of the query results. That's because

it is only added if you specify a custom routing value when you index documents.

That's essentially what routing is. Before ending this lecture, I want to tell you something

interesting. Do you remember how the number of shards cannot be changed once an index

has been created? If we take another look at the routing formula, then we can actually

see why. Because the number of shards is used within the formula. If we were to change the

number of shards for an index, then the routing formula would yield different results. That's

not a problem when indexing new documents, but it would cause big trouble for existing documents.

Suppose that we have an index with two shards, and that we indexed a document. Shard number

two was the result of the routing formula, so that's where the document was stored.

Let's imagine for a moment that we were able to increase the number of shards for

the index, and that we increased it to five. We index some more documents, and all is well.

However, when we try to retrieve specific documents based on their ID, Elasticsearch

is sometimes unable to locate the documents. That's because a document's ID is then

run through the routing formula again, and since one of the formula's variables has

changed, the result may be different. If that is the case, Elasticsearch tries to find a

document on the wrong shard, and thus returns an empty response, even though the document

does exist. That's not the only reason why additional shards cannot be added to an index, though.

Another problem could be that an index' documents would be distributed very unevenly.

As a quick reminder, modifying the number of shards requires creating a new index and

reindexing documents into it. That's made fairly easy with the Shrink and Split APIs,

though. Anyway, that was just a little bit of bonus information for you.