---
id: rg04dryuvjx1xz65i998g0i
title: 76_QueryingForFieldExitence
desc: ''
updated: 1701194048608
created: 1701109410110
---
In this lecture we are going to take a look at how to search for

documents based on whether or not they contain a value for a field.

We can accomplish this with the exists query by simply specifying the name of the field. If you

have worked with relational databases before, this is similar to the IS NOT NULL condition.

Note that we have two mappings for the tags field.

Although I chose the tags.keyword mapping in this example, the result would be exactly the

same with the tags mapping. I will be using the keyword mapping throughout this lecture.

Anyway, I have prepared the search query within the Console tool, so let’s go ahead and run it.

You might be surprised to see that this query only matches 554 documents, even though all

documents contain a value for the tags field. That’s because the query matches documents

that contain an indexed value for a field. If we supply an empty value for a field when indexing

a document, this value is stored as part of the _source object, but no value is indexed.

An empty value is either NULL or an empty array. Notice how an empty string is not considered an

empty value, meaning that documents with this value would be matched by the exists query.

With our data set, some documents don’t have any tags and therefore contain an empty array as the

value for the field. When these documents were indexed, no values were indexed into

the inverted index for the field. Let’s look at an example where two documents are indexed.

Both documents contain a value for the name field, causing both values to be analyzed and

indexed into the inverted index for the field. For the tags field, however, only one of the documents

contain a non-empty value, being document #1. While document #2 did specify a value,

an empty array is treated as if no value was provided, and so nothing was added to the field’s

inverted index. If we were to run an exists query on the tags field, only document #1 would match.

Besides empty values, there are a few other reasons why a document might not

have an indexed value for a field. Let’s take a quick look at those.

First, I want to mention an exception when providing an empty value.

If a value of NULL is provided for a field and its mapping contains the null_value parameter,

the value of this parameter will be indexed.

This means that the document will match an exists query, because a value was indexed.

A common reason why no value is indexed is that no value was provided for the

field at all, meaning that the field was missing from the indexing request.

The index mapping parameter can also be set to false within the mapping,

in which case the field’s values will not be indexed.

As mentioned earlier in the course, this parameter is often used for time series data. That’s because

you might have numeric fields that you won’t use for filtering, but rather for aggregations.

That’s just to say that using this parameter is not as uncommon as it may seem.

It’s also possible that the ignore_above parameter is used, and the value is too long,

causing it to be ignored in terms of indexing. Remember how this parameter defaults to 256 for

keyword fields when dynamic mapping is used. A value with a length of 400, for instance,

would be ignored for the keyword mapping but still indexed for the text mapping.

Last but not least, the mapping might have the ignore_malformed parameter set to true.

If this is the case, trying to index a string into a numeric field would

cause the value to be ignored, i.e. not indexed.

If this parameter is not specified within the mapping, that indexing request would fail instead.

By far the most common reasons for there to be no indexed value are

that an empty value was provided, or no value was provided at all.

Alright, just one thing left to cover in this lecture. You might have wondered how to invert

the query, i.e. to search for documents that don’t contain an indexed value for a field.

There is no dedicated query for doing exactly that.

Instead, we need to use another query named bool. This query lets us specify

other queries that must or must not match. Consider the following query, for example.

The exists query is added to the must_not parameter of the bool query.

This means that a given document will only match if it does not have a value

indexed for the tags.keyword mapping. That’s exactly what we were looking for.

We haven’t covered the bool query yet, but we will get to it a bit later, so don’t worry

about the specifics for now. I just wanted to show this query to you now for reference.

Let’s quickly run this query within the Console tool.

As you can see, this query matched 446 documents. The

other one matched 554 documents. This totals our 1,000 documents.

If I scroll down through the results, we will see that the documents all contain an

empty array for the tags field, hence why no value was indexed.

And that’s the end of this lecture. I’ll see you in the next one.