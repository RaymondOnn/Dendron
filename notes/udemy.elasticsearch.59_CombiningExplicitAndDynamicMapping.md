---
id: 5rqjyu7a15ivu5kqtzqyr4a
title: 59_CombiningExplicitAndDynamicMapping
desc: ''
updated: 1701193614334
created: 1701105158785
---
Now that you have seen both explicit and dynamic mapping, which one should you choose, then?

I will give you some recommendations in a couple of lectures, but you don’t necessarily

have to choose one or the other.

That’s because the two approaches can actually complement each other.

Let me show you an example.

I have prepared a couple of queries in advance.

The first one creates a new index named “people” with a “text” mapping for a “first_name” field.

Nothing new here.

I have run that query in advance, and you can see the mapping to the right.

Let’s now index a document containing both a first and last name.

There is no mapping for the “last_name” field, so what do you think will happen?

I’m sure you guessed it, but let’s see.

Let’s then retrieve the mapping again.

Perhaps unsurprisingly, the “last_name” field has now been mapped.

That’s because dynamic mapping is enabled by default and a mapping didn’t exist already.

Elasticsearch will then create a field mapping automatically in exactly the same way as you

saw in the previous lecture.

The difference here is just that we already had mappings, just not for that particular field.

That’s really all I wanted to show you in this lecture; that explicit and dynamic mapping

can be combined, so you don’t necessarily have to choose one or the other.