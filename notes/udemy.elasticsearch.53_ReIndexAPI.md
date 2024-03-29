---
id: 6i6ubhnoujyah97z2mmig7d
title: 53_ReIndexAPI
desc: ''
updated: 1701193484312
created: 1701104968991
---
Alright, so as we talked about in the previous lecture, changing how documents are mapped

usually requires reindexing documents.

The first step is to create a new index and define the new mapping and optionally any

index settings.

Let’s create an updated version of the “reviews” index where the “product_id” field is

of the “keyword” data type.

To save some typing, let’s retrieve the current mapping and copy/paste that into

the query.

All we need to do now is to modify the data type

for the “product_id” field.

That’s it.

Let’s run the query.

The index has now been created and we are ready to add documents to it.

But how do we do that?

We could write a script that retrieves documents from the old index, modifies them in one way

or another, and then indexes them into the new index.

That takes quite a bit of work, especially if we want to reindex a large number of documents.

Instead, Elasticsearch exposes a convenient API specific to this purpose, namely the Reindex

API.

This API does the heavy lifting of moving documents from one index to another so that

we don’t have to.

That’s because reindexing documents is actually a pretty common thing to do during the lifetime

of an index.

Business requirements may change, or perhaps we need to tweak how data is stored when scaling

from thousands to hundreds of millions of documents.

Whatever the case, let’s begin typing out the query that moves documents from the old

index into the new one.

The HTTP verb is “POST” and the endpoint is simply “_reindex.”

We will need to define two parameters named “source” and “dest,” both being objects.

Let’s add those to the request body.

The “source” parameter defines where the documents should be retrieved from and how

to retrieve them.

For now, let’s just define the index name within a parameter named “index.”

Next, let’s define where the documents should be stored, which we also specify with an “index”

parameter, this time within the “dest” parameter.

That’s the basic query for reindexing documents.

Let’s run it and see what happens.

Everything appears to have worked, and the results state that five documents were reindexed.

Let’s retrieve the documents within the new index to verify that everything looks index.

Indeed the five documents are returned.

At a glance, everything looks good, but if we take a closer look, notice how the values

for the “product_id” field are numeric and not strings.

To be clear, this has no effect on how the values are indexed - specifically in regards

to the inverted index.

This is only a cosmetic thing within the “_source” object.

Remember that this object contains the original values that were supplied at index time.

Us changing the data type doesn’t cause the “_source” object to be modified.

However, when performing search requests, you will typically grab field values from

the “_source” object, and it would be fair to assume that you would get a string

value back for a “keyword” field.

That’s why it might be a good idea to modify the source document while reindexing the documents.

Alternatively you can just convert the data type within whatever application is querying

the index, but I prefer for Elasticsearch to return the expected data type.

The way that we can modify documents during reindexing, is to supply a script that is

run for each document.

I will just paste in a query with the script to save a bit of typing.

The script is defined within a “source” parameter, which is nested within the “script”

parameter.

We haven’t talked much about scripting yet, although you have seen some simple examples

earlier.

That’s why this example is also a very simple one.

It checks if the “product_id” field is defined within the source document, first

of all.

That’s because our reviews didn’t contain a product ID from the beginning, so one of

our documents does not contain this field.

If we left out this check, we would get a NullPointerException for that document.

Anyway, the following line simply replaces the value of the “product_id” field within

the source document with the existing value converted to a string.

Let’s delete the documents within the new index and reindex the documents again.

To do that, we will use the Delete by Query API.

I have prepared the query in advance, so let’s just go ahead and run it.

Now that the documents have been reindexed once

again, let’s verify that the product IDs have been converted to strings within the

“_source” object.

And indeed they have.

Awesome!

Alright, let me show you a couple of slightly more advanced use cases.

We’ll be leaving Kibana for this, because I don’t want to spend a lot of time typing

out queries when I can just show them to you on a slide.

If you want to take them for a spin, by all means go ahead and run them within Kibana.

You can find the queries within the GitHub repository so that you can copy and paste

them.

The first thing I will show you is that we can specify a query within the “source”

parameter to only reindex documents that match the query.

That’s useful if we only want to reindex a subset of the documents that are stored

within the source index.

Since we haven’t covered search queries yet, I have just specified the “match_all”

query.

That doesn’t really make sense because that’s the same as not specifying a query.

Let’s make it a bit more interesting and meaningful by specifying a query that matches

reviews with a rating of at least 4.0.

Don’t worry about the specifics of the query, although it’s actually pretty straightforward.

Running this reindex query would only reindex reviews with a rating of at least 4.0.

Pretty cool, right?

Next, suppose that we want to remove a field.

As you know, we cannot delete a field mapping, so that is not an option.

As you also know, we can just leave out the field when indexing new documents and leave

things as is.

However, if we have indexed lots of documents, perhaps we want to reclaim the disk space

used by the field.

Remember that even though we stop supplying a value for a field when indexing new documents,

the existing values are still maintained within a data structure.

Depending on how many values have been indexed, this may use a considerable amount of disk

space that we could use for other things.

That’s of course assuming that we won’t need to query these values in the future.

The way we can do this is to use so-called “source filtering.”

By specifying an array of field names, only those fields are included for each document

when they are indexed into the destination index.

In other words, any fields that you leave out will not be reindexed.

It’s possible to do the same thing with a script, but using the “_source” parameter

is a simpler approach.

Sometimes you might want to rename a field, such as renaming the “content” field to

“comment.”

That can be done with a script consisting of just one statement.

In case you were wondering, the “_source” key translates to a Java HashMap.

Besides removing a key, the “remove” method also returns its value, which is why the assignment

works.

Just as with scripted updates, it’s possible to specify the operation for the document

within the script.

That’s done by assigning a value to the “op” property on the “ctx” variable.

You can assign a value of either “noop” or “delete.”

In the case of “noop,” the document will not be indexed into the destination index.

This is useful if you have some advanced logic to determine whether or not you need to reindex

documents.

In this example we only reindex reviews with a rating of at least 4.0.

Note that it will usually be enough to define a query that matches the documents, but for

advanced use cases that might not always be possible.

In this particular example, we could just as well have defined a query that expresses

this constraint, which you actually saw how to do earlier.

That approach is actually much better in terms of performance, so you should always prefer

specifying a query whenever possible.

Setting the operation to “delete” causes the document to be deleted within the destination

index.

This can be useful because the document might already exist within that index.

In this lecture we reindexed documents into an empty index, but that might not always

be the case; there is nothing preventing us from using the Reindex API to move documents

into a non-empty index.

The API is just typically used for new indices when changing mappings, but it is not limited

to just that use case.

That being said, you can typically achieve the same thing by using the Delete by Query

API instead.

There are more parameters available for the Reindex API than the ones we covered in this

lecture.

For example, you can define how to handle version conflicts, just as with the Update

by Query API.

That’s because the Reindex API also makes a snapshot of the index before indexing documents

into the destination index.

By default, the operation is aborted if a version conflict is encountered.

That’s fine when the destination index is empty, but that might not be the case.

Just like the Update by Query and Delete by Query APIs, the Reindex API also performs

the operations in batches by using the Scroll API.

That’s essentially how it’s able to handle millions of documents efficiently.

You can define parameters that are related to throttling and limiting the number of requests

per second.

That’s useful if you need to reindex a lot of documents on a production cluster and you

want to limit the performance impact.

Anyway, that all becomes more complicated than what I want to cover here.

The point is that if you intend to use this API for millions of documents at once, then

I recommend that you take a look at the documentation attached to this lecture.