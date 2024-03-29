---
id: 18ouxg3xkcjypn21htqnzv4
title: 40_IntroToMapping
desc: ''
updated: 1701104513502
created: 1701085037352
---

Now that you know the basics of text analysis and how the resulting terms are stored, let’s

move on to talking about mapping.

We will revisit the concept of analysis, because as you will soon find out, mapping and analysis

are quite closely related.

So what is mapping all about?

Mapping defines the structure of documents and how they are indexed and stored.

This includes the fields of a document and their data types.

As a simplification, you can think of it as the equivalent of a table schema in a relational database.

On your screen, you can see how a simple table in a relational database could be mapped in Elasticsearch.

There is more to Elasticsearch mapping than this, so this is just a way for you to wrap

your head around the general idea of what mapping is.

Simply defining the fields and their data types is not that exciting, so luckily there

is much more to it than that.

Mapping is actually pretty cool and not quite as boring as it sounds.

In Elasticsearch, there are two basic approaches to mapping; explicit and dynamic mapping.

With explicit mapping, we define fields and their data types ourselves, typically when

creating an index.

That’s exactly what you saw on the diagram a moment ago.

To make Elasticsearch easier to use, a field mapping will automatically be created when

Elasticsearch encounters a new field.

It will inspect the supplied field value to figure out how the field should be mapped.

If you supply a string value, for instance, Elasticsearch will use the “text” data

type for the mapping.

As you will see, explicit and dynamic mapping can actually be combined, so mapping is quite

flexible in Elasticsearch.

Anyway, let’s get started!