---
id: l3lm23lt65rewerzvt7z3jv
title: 50_MissingFields
desc: ''
updated: 1701104767549
created: 1701085325122
---

I still have the search results from the previous lecture opened up, because I want to point something out.

Perhaps you already noticed how the first document doesn’t contain the “created_at” field.

That’s because we indexed it before realizing that we need the timestamps of when reviews

were written.

Remember how I said that fields may have zero or more values by default?

This means that all fields are actually optional and you are free to leave them out completely

when indexing documents.

That’s different from relational databases where you need to explicitly allow NULL values.

But isn’t this inconvenient, you might wonder?

Well, I suppose that’s a matter of preference.

It does give you more flexibility out of the box, but it also means that some integrity

checks need to be done at the application level.

Elasticsearch will validate field values against any mapping that might exist, but it won’t

reject documents that leave fields out.

To be clear, you can leave out fields even if a mapping exists for it, so adding a field

mapping does not make the field required.

This means that you should handle this at the application level.

There are some workarounds for doing this in Elasticsearch, but they are neither pretty

nor convenient.

When searching for documents, you just specify field names as normal, and Elasticsearch will

only consider documents that contain a value for the field.

In other words, you don’t need to actively handle if some documents may have left fields out.

That’s all I wanted to mention in this lecture, so I’ll see you in the next one!