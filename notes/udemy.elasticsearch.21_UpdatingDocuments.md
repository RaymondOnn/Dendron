---
id: wk18l4s9yi6asf2iiz8hzvc
title: 21_UpdatingDocuments
desc: ''
updated: 1701103967777
created: 1701080685514
---
Now that we have some documents within our index, let's take a look at how we can update them.

Suppose that a product has been sold, and we therefore need to decrease the "in_stock"

field by one.

We can do that by sending a POST request to the Update API, following the document's

ID.

Let me just type that out.

Within the request body, we should add a "doc" object consisting of key-value pairs.

The keys are the field names, and the values are the new field values.

Let's set the "in_stock" field to 3, where it used to be 4.

The document has now been updated, which we can tell because the "result" key has

a value of "updated." This key might also have a value of "noop" if the update did

not result in a change, i.e. if we supplied the same value as the document currently contained

for the field.

Let's retrieve it to confirm that the "in_stock" field now has a value of 3.

Indeed the field value has been updated as we expected.

Apart from updating existing fields, we can also add new fields to existing documents.

We do that in exactly the same way, so let's make a copy of the query we just wrote and

define a new field.

The new field will be a "tags" field containing an array of string values.

Note that I could just have added the "tags" key to the first query that we ran.

Any field that does not exist already is added, so the result would be the same.

Let's quickly verify that the document now contains a field named "tags."

Indeed we now see the "tags" field within the "_source" key.

That's how easy it is to update Elasticsearch documents!

Before ending this lecture, I want to take a short moment to talk about how this works

internally.

It might surprise you that Elasticsearch documents are immutable.

What this means, is that documents cannot be changed.

But didn't we just change an existing document?

Actually we didn't.

We replaced it.

The Update API just took care of a couple of things for us.

Specifically, it retrieved the document, changed its fields according to our specification,

and reindexed the document with the same ID, effectively replacing it.

So to be clear, the existing document was not updated with any new fields or values;

it was replaced entirely.

It just appeared as if the document was updated.

We could have done the exact same thing by first retrieving the existing document, then

modify its fields, and then finally replace the document that we retrieved in the first

place.

What the Update API does, is just save us some work.

On top of that, it is also more efficient, because it limits the number of network round

trips that are required.

Everything happens with a single request within the shard on which the document is stored.

If we were to do this ourselves, we would need to send two requests instead of one,

and thereby incur some additional network latency and overhead.

That was just a bit of background knowledge for you.