---
id: g13g4ziecv9uz6rvvw8j70y
title: 51_MappingParameters
desc: ''
updated: 1701104796651
created: 1701085366874
---
You have seen how to add a simple mapping to an index, specifying fields and their data types.

Apart from specifying a field’s data type, there are also some parameters available that

configure the behavior of fields in various ways.

In this lecture I will give you an overview of some of the most important parameters.

Please note that this is not an exhaustive list of parameters, so there are other ones

available as well.

Most of them are either rarely used or quite advanced, though.

I will come back to a couple more parameters throughout the rest of this section when the

time is right.

With that said, let’s get started!

The first parameter is the one named “format.”

This parameter is used to specify a date format when you want to index dates that are not

formatted in the default format.

I recommend that you use the default date format whenever possible.

In particular the ISO 8601 format is an extremely common format that should be supported by

almost all systems.

It might not always be possible to format dates in one of the default formats, though;

perhaps you are integrating with a legacy system that’s just not feasible to change.

Oftentimes you will have an event processing pipeline such as Logstash that can reformat

data, but that might not always be the case.

That’s just to say that sometimes it might be necessary to use non-standard date formats.

We can completely customize the date format by specifying a format that is compatible

with Java’s DateFormatter class.

Alternatively there are a number of built-in formats available.

Two of them are actually used within the default format that Elasticsearch uses.

There is a rather long list of other built-in formats that we can conveniently use instead

of building our own pattern.

An example could be if we wanted to specify a UNIX timestamp instead of the number of

milliseconds since the epoch, in which case we could use the “epoch_seconds” format.

I have attached a link to the documentation in case you want to check which formats are available.

Alright, so that’s the “format” parameter.

The next parameter is actually one that you have seen before; namely the “properties”

parameter.

We use it both at the top level when defining mappings, and for nested fields.

To be clear, nested fields can be for both fields of the “object” and “nested”

data types, which you can see examples of on your screen.

Remember that even though I just mentioned an “object” data type, there is no such

data type in Elasticsearch; instead, “object” fields are mapped implicitly by using the

“properties” parameter instead of specifying a type.

I won’t spend any more time on this parameter, because you have already seen it in action,

and there is really nothing more to it.

Next up is the “coerce” parameter, which should also be familiar to you.

Although you haven’t seen the parameter before, we have covered the concept of coercion.

The parameter lets you enable or disable type coercion.

It’s enabled by default, but that might not always be desirable.

Whether or not you want it to be enabled is really a matter of preference and how strict

you want Elasticsearch to be when indexing documents.

You can see an example of how to disable coercion on your screen now.

If we were to supply a string value for the “amount” field, Elasticsearch would reject

the document.

It’s also possible to configure coercion at the index level so you don’t have to

specify the “coercion” parameter for every field.

Since coercion is enabled by default, it only really makes sense to disable it at the index

level.

Fields inherit this index level setting, but you can still overwrite it if you want to.

In the example to the right, coercion is disabled at the index level, but the “amount” field

overwrites this and enables coercion for that field.

Typically you will have fields that don’t specify the “coerce” parameter and thereby

use the index level setting implicitly.

I just didn’t include such fields to keep the example short, but I’m sure you get

the point.

Next is a parameter named “doc_values.”

This one requires us to dive a bit deeper into Apache Lucene.

I told you what an inverted index is and how that is used to efficiently resolve which

documents contain a given term.

I also told you that this is not the only data structure used for fields.

That’s because there is no single data structure that efficiently serves all purposes.

For instance, while an inverted index is excellent for searching for terms, it doesn’t perform

well for most other use cases.

There is more to queries than simply searching for data; perhaps we want to sort the results

alphabetically, or to aggregate values.

These are two examples of when an inverted index does not perform well, because the access

pattern is different.

Instead of looking up terms and finding the documents that contain them, we need to look

up the document and find its terms for a field.

This is what the “doc values” data structure is; pretty much the opposite of an inverted index.

Besides being used for sorting and aggregations, this data structure is also used when accessing

field values from within scripts.

To be clear, the “doc values” data structure is not a replacement for an inverted index,

but rather an addition to it, meaning that both data structures will be maintained for

a field at the same time.

Elasticsearch will then query the appropriate data structure depending on the query.

Why am I telling you about this data structure now?

Because you have the option of disabling it with the “doc_values” mapping parameter.

But why and when would you do this?

The main reason for doing this would be to save disk space, because this data structure

would then not be built and stored on disk.

Storing data in multiple data structures effectively duplicates data with the purpose of fast retrieval,

so disk space is traded for speed.

A side benefit of that would be increased indexing speed, because there is naturally

a small overhead of building this data structure when indexing documents.

So when would you want to disable doc values?

If you know that you won’t need to use a field for sorting, aggregations, and scripting,

you can disable doc values and save the disk space required to store this data structure.

For small indices, it might not matter much, but if you are storing hundreds of millions

of documents, there will be a significant difference.

It’s important to note that if you disable doc values, you cannot change this without

reindexing all of your data.

This actually applies to many mapping parameters as you will see soon.

This means that you really have to consider which kinds of queries you run against your

data and try to anticipate how you will query your data in the future.

This is also why you should consider this an advanced parameter, and you should probably

not use it unless you have a reason for doing so.

Typically that would be if you are storing a large number of documents, or if you anticipate

that you will.

Before continuing, here is how doc values can be disabled within a field’s mapping.

Next up, we have a parameter named “norms.”

We haven’t covered what norms are yet, so I will just give you a quick introduction here.

Norms refers to the storage of various normalization factors that are used to compute relevance scores.

We haven’t gotten into search queries yet, but oftentimes we will want to not just apply

a filter to documents, but also rank them based on how well they match a given query.

Think of search results on Google, for instance.

The results on page five are naturally not as relevant as the ones on the first page.

That’s because they are sorted based on relevance, and the same is done for some queries

in Elasticsearch.

Part of what enables Elasticsearch to calculate relevance scores for documents, is these norms.

As with many other things, they are stored within Apache Lucene, but that’s besides

the point.

What the “norms” mapping parameter enables us to do, is to disable these norms.

Why?

Because they take up quite a lot of disk space, just like doc values do.

Not storing norms saves disk space, but also removes the ability to use a field for relevance scoring.

That’s why you should only disable norms for fields that you won’t use for relevance scoring.

For example, you might have a “tags” field for products as you saw earlier in the course.

Such a field would almost always be used for either filtering or aggregations.

We can use the field for these use cases just fine, because they don’t involve relevance scoring.

In such a scenario, it makes perfect sense to not store the norms at all, because they

are just a waste of disk space.

A “name” field for a product, however, would almost always be used for relevance

scoring because we would probably be performing full-text searches on the field.

So if you know that you will only use a field for filtering and aggregations, then you can

save disk space by disabling norms.

I hope that makes sense.

It’s also possible to ignore a field in terms of indexing, by setting the “index”

parameter to “false.”

By doing so, the field values are not indexed at all, and the field therefore cannot be

used within search queries.

It’s important to note that the field value will still be stored within the “_source”

object; it just won’t be part of the data structures used for searching.

This parameter is useful if you have a field that you don’t need to search, but you still

want to store the field as part of the “_source” object.

Not indexing a field naturally saves disk space and slightly increases the rate at which

documents can be indexed.

This parameter is often used for time series data where you have numeric fields that you

won’t use for filtering, but rather for aggregations.

Even if you disable indexing for a field, you can still use it for aggregations.

That’s because those work a bit differently, but I don’t want to get into that right now.

Just two more parameters, so hang in there!

In a previous lecture, we talked about how missing field values are handled, i.e. if

a field is left out when indexing a document.

But what if we include the field and supply NULL as the value?

NULL values are ignored in Elasticsearch, meaning that they cannot be indexed or searched.

The same applies to an empty array, or an array of NULL values.

If you want to be able to search for NULL values, then you can use the “null_value”

parameter to replace NULL values with a value of your choice.

Whenever Elasticsearch encounters a NULL value for the field, it will index the value specified

by the parameter instead, thereby making this value searchable.

There are a couple of things to note about this parameter.

First, it only works for explicit NULL values, meaning that providing an empty array does

not cause Elasticsearch to perform the replacement.

Secondly, the value that you define must be of the same data type as the field’s data

type.

If the field is an integer, then you must supply an integer value to the parameter.

Lastly, the parameter influences how data is indexed, meaning the data structures that

are used for searches.

The “_source” object is therefore not affected, and will contain the value that

was supplied when indexing the document, i.e.

NULL.

Time for the last parameter - I promise!

Suppose that we have the name of a person stored as two fields; “first_name” and

“last_name.”

While we can query one or both of these fields just fine, we might also want to index the

full name into a third field, perhaps named “full_name.”

That’s possible with the “copy_to” parameter.

The value for the parameter should be the name of the field to which the field value

should be copied.

So by adding the parameter to both the “first_name” and “last_name” fields, Elasticsearch

will copy both values into the “full_name” field.

Notice how I said that values will be copied.

That’s because it’s not the tokens or terms yielded by the analyzer that are copied;

instead, it’s the values that were supplied.

These values are then analyzed with the analyzer of the target field.

One important thing to note is that the copied values will not be part of the “_source” object.

In this example it means that the “full_name” field is not part of the “_source” object,

since the values are only indexed.

The exception to that is if you specify a value for the “full_name” field when you

index the document, but that’s not a very common thing to do at all.

Alright, that’s the end of this overview.

As I mentioned at the beginning of the lecture, we didn’t cover all mapping parameters here.

We will get back to a couple more important ones throughout the remainder of this section.

The chances that you will use the rest are pretty slim, so I will leave you to explore

those within the documentation if you want to.