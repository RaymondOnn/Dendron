---
id: lndoga27bz9lvgqzsdrj97g
title: 48_AddingMappingToExistingIndices
desc: ''
updated: 1701104718565
created: 1701085276093
---
You know how to define mappings, but I only showed you how to define them for a new index.

What if we already have an index and we want to add a new field?

That’s what we’ll cover in this lecture.

We added a “reviews” index and defined the mappings for it, but come to think about

it, we probably want to save the timestamp of when the review was written as well.

Elasticsearch does not store the timestamp of when the document was indexed, so we need

to do that ourselves.

Let’s add the mapping for a “created_at” field.

We will still use the PUT HTTP verb, but this time we will invoke the Mapping API.

Since we are using the Mapping API, we should not specify the “mappings” key as we did

when creating the index.

Instead, we specify the “properties” key at the top level of the request body.

The rest of the syntax is the same, so let’s type everything out.

We’ll talk more about the “date” data type in the next lecture, so let’s just

add the mapping for now.

The mapping has been added successfully.

Let’s retrieve the mapping for the index just to be sure.

And indeed we see the field mapping within the results.

That’s how easy it is to add mappings to an existing index.

Let’s take a closer look at the “date” data type now.