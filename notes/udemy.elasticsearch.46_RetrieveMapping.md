---
id: yedfgsgh5lcm2x53q6dmkbl
title: 46_RetrieveMapping
desc: ''
updated: 1701104656104
created: 1701085204628
---
Now that we have created a mapping for the “reviews” index, I want to quickly show

you how to retrieve mappings.

Since we specified the mapping ourselves in the previous lecture, there won’t be any

surprises here.

However, when using dynamic mapping, it may sometimes be useful to check how Elasticsearch

has mapped fields based on the documents that have been indexed.

To retrieve the mapping for an index, we can send a GET request to the Mapping API as follows.

The results essentially show the same thing that we typed out in the previous lecture.

In addition to retrieving mappings for the entire index, it is also possible to retrieve

the mapping for a specific field.

To do that, we simply use the “field” command, followed by the name of the field.

Let me just type that out so you can see what it looks like.

For objects, you can specify the path of a key using a dot notation, such as “author.email.”

That was pretty easy, right?

Now that you know how to retrieve an index’ mapping, let’s continue.