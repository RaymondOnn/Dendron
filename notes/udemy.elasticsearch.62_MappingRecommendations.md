---
id: vz9om1h9xch0efxqt2k4d9n
title: 62_MappingRecommendations
desc: ''
updated: 1701193711168
created: 1701105296807
---
Before we get back to analyzers, I want to give a couple of recommendations in regards

to mapping.

Some of them are my own personal opinions, so whether or not you want to follow them

is of course totally up to you.

First and foremost, I recommend that you make use of explicit mapping - at least for production clusters.

Dynamic mapping is convenient during development, but the generated field mappings are often

not the most efficient ones.

If you intend to store a high number of documents, you can typically save a lot of disk space

with more optimized mappings, for instance.

By using explicit mappings, I mean to set the “dynamic” parameter to “strict”

and not just “false.”

Setting it to “false” will enable you to add fields for which there are no mappings.

The fields are then simply ignored in terms of indexing.

Querying such fields won’t match anything, which might lead to unexpected search results.

That’s why I recommend that you set the “dynamic” parameter to “strict,” because

then you cannot end up in a situation like that.

Using strict mapping essentially means that you are always in control.

Another related recommendation is to not always map “text” fields as both “text” and

“keyword” unless you need to do so.

This happens with dynamic mapping by default, but it is often not necessary and just takes

up additional disk space.

If you disable dynamic mapping, you should ask yourself how you intend to query a given field.

Do you want to perform full-text searches on it?

Then you should add a “text” mapping.

Do you want to use the field for aggregations, sorting, or filtering based on exact values?

Then you should add a “keyword” mapping.

If you want to do both, you should map the field in both ways, but that is typically

not necessary.

If you have full control of whichever application is sending data to Elasticsearch, then I recommend

that you disable coercion as well.

Type coercion is convenient, but it is essentially a way for Elasticsearch to forgive you for

not doing the right thing.

Whenever possible, always provide the correct data types in the JSON objects that you send

to Elasticsearch.

If you add explicit field mappings for numeric fields, then you should consider which kind

of numbers you will index.

Sometimes it is not necessary to map a field as “long,” because an “integer” might be enough.

The former data type can hold larger numbers, but also requires more disk space.

Likewise, “double” requires twice as much disk space as “float,” so if you don’t

need really high precision, then you should be fine using “float.”

There are a number of mapping parameters that you can use to limit the amount of data that

Elasticsearch stores.

Actually it’s Apache Lucene that stores this data, but that’s besides the point.

We already discussed them, so this is just a quick reminder.

If you know that you won’t use a field for sorting, aggregations, and scripting, you

can set “doc_values” to “false” and save quite a bit of disk space.

If you won’t use a field for relevance scoring, then you can set “norms” to “false”

to avoid storing data that is only used for relevance scoring.

Lastly, if you won’t use a field for filtering, you can disable indexing by setting “index”

to “false.”

The field can still be used for aggregations, so this is a common thing to do for time series data.

These parameters are useful when storing lots of documents, and the savings can be significant.

If you only need to store a low number of documents, then it’s probably not worth

the effort to deal with these parameters; the savings would be limited, and you might

be overcomplicating things.

There is no rule as to when it’s worth it to use these parameters and when it isn’t.

As a rule of thumb, though, it’s probably not worth it for less than a million documents.

If you know that you will need to store many million documents when defining your mappings,

then you should optimize the mappings from the beginning.

That way you won’t need to reindex documents into a new index later on.

If you don’t do it from the beginning, the worst that can really happen is that you will

need to reindex documents sometime, so it’s really not the end of the world.

There are more optimizations that you can do depending on your use case, but these are

the most common ones.

I don’t want to go into a lot of detail and bore you with edge case optimizations,

so these are my general recommendations when it comes to mapping fields.