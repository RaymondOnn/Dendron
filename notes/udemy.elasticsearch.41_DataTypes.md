---
id: z09n9ky11day6a9xwa0cdzw
title: 41_DataTypes
desc: ''
updated: 1701104533388
created: 1701085057534
---
Before we really get into mapping, I want to quickly give you an overview of the available data types.

There are quite a few of them, so I will just highlight the most important ones.

On your screen, you can see some of the basic Elasticsearch data types.

You probably recognize some of them from various programming languages, | but either way, their

uses should be pretty obvious.

Apart from these, there are a number of more specialized data types, such as an “ip”

data type for storing IP addresses.

Some of the specialized data types are related to specific Elasticsearch features, such as

for auto-completion or geospatial searches.

Instead of talking about all of them now, I will introduce them throughout the course

as they become relevant.

That being said, I do encourage you to check out the list of data types within the documentation,

just so you know which ones are available.

There are some pretty cool ones for specific use cases that I won’t have time to cover

in this course.

I’ve attached the link to this lecture.

Instead of spending time on covering these basic data types extensively, let’s move

on to some data types that are a bit more complex.

First, we have the “object” data type.

This data type essentially just covers any JSON object.

Each document that we index into Elasticsearch is a JSON object.

This document may then contain a field with an object as its value.

This object may then contain an inner object itself, forming an object hierarchy.

In other words, you can nest objects.

The way this is mapped, is that a “properties” key is added for objects instead of specifying

the “type” key as with other data types.

The type is therefore not just set to “object” as you might have expected.

“properties” is actually a mapping parameter, but we will get to that soon.

We then define the keys that the object contains within this “properties” key.

The same is the case if this inner object contains an inner object itself.

Notice how we also did this for the fields at the root level of the mapping.

Don’t worry if you find this syntax confusing at first; it does take a bit of time to get used to.

Even though we just define objects as regular JSON objects when we index documents, that’s

actually not how the objects are stored internally.

As you know, Elasticsearch is built on top of Apache Lucene, which does not support objects.

That’s why Elasticsearch transforms inner objects to a format that is compatible with

Lucene, as part of indexing operations.

The reason for that is to ensure that we can index any document that is valid according

to the JSON specification.

This makes Elasticsearch easier to use, because we don’t need to structure documents in

any special way to ensure that they can be indexed into Apache Lucene.

The way the objects are stored internally, is that they are flattened.

Each level in the hierarchy is denoted with a dot, such that there are no longer any objects,

but the hierarchy is maintained.

This way, object fields can now be stored within Apache Lucene, and we can still query

fields as if they were objects by using a dot notation syntax.

That’s great, but what happens if we try to index an array of objects?

How are the objects flattened then?

Wouldn’t the result be duplicate field names?

The answer to the last question is yes.

What happens is that the values are grouped by field name and indexed as an array.

If you run a search query against one of the fields, it will search through all of the

values within the array.

That’s pretty nice in some situations, but it can cause trouble in others.

Let’s look at an example.

Suppose that we want to search for products that have a review left by a person named

John Doe, with a rating of at least 4.

The query is pretty simple, but since we haven’t gotten to search queries yet, the one you

will see is just a pseudo query.

What will happen, is that the product will match, even though the review left by John

Doe actually has a rating of 3.5.

The reason is that the field values were all mixed together when the document was indexed,

so the relationship between the object keys was lost.

When this happens, Elasticsearch doesn’t know that there is a relationship between

“John Doe” and 3.5.

What we intended to do, was to match products containing a review written by John Doe and

with a rating of at least 4.

What actually happened was that we matched products containing a review written by John

Doe or with a rating of at least 4.

This essentially means that the boolean AND was effectively turned into a boolean OR due

to how the values are stored internally.

That’s probably not what we want, and it’s definitely not the behavior that you would

To solve this, there is a data type called “nested,” which is a specialized version

of the “object” data type.

The purpose of it is to maintain the relationship between object values when an array of objects

is indexed.

Using this data type enables us to query objects independently, meaning that the object values

are not mixed together as you saw a moment ago.

To utilize this data type, we must use a specific query, which you will see later in the course.

On your screen you can see an example of using this data type.

The data type is simply set to “nested” in the same way as for other data types.

Using the pseudo query from before, it would only match products containing a review written

by John Doe and with a rating greater than or equal to 4.

That’s because the objects are stored independently and the query then behaves how we probably

have expected it to in the first place.

Unlike before, the product is therefore not matched by the query.

I mentioned that Apache Lucene has no concept of objects, so how are these objects stored?

They are actually stored as hidden documents.

And yes, by documents, I mean documents just like the ones we indexed.

The difference is that these documents won’t show up in search results unless we query

them directly.

Suppose that we index a product containing ten reviews, each being a nested object.

This would cause 11 documents to be indexed into Lucene; one for the product, and one

for each review.

The last data type I want to talk about is the one named “keyword.”

This data type should be used for fields on which you want to search for exact values.

Since only exact searching is supported, this data type is used for filtering, sorting,

and aggregating documents.

An example would be to have a “status” field for news articles and search for all

articles that have a value of “published” for the field.

If you want to perform so-called full-text searches, you need to use the “text” data

type instead.

Full-text searches are basically searches that do not require exact matches.

For instance, we could search for news articles containing one or more specific words within

their contents.

We will dive deeper into the difference between the “text” and “keyword” data types

in the next lecture.

The takeaway for now is that the “keyword” data type is used for sorting, filtering,

and aggregations.

That’s a quick overview of the most important data types in Elasticsearch.

Like I said, I encourage you to check out the documentation to see which ones are available.