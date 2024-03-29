---
id: a5h57vyre315j15d3n8yvtg
title: 55_MultiFieldMappings
desc: ''
updated: 1701193523866
created: 1701105021663
---
It may come as a surprise to you that a field may actually be mapped in multiple ways.

For instance, a “text” field may be mapped as a “keyword” field at the same time.

That’s what we will look at in this lecture.

I have a simple query prepared, which creates a throwaway index with two fields; “description”

and “ingredients.”

As a quick reminder, remember how every field in Elasticsearch may store zero or more values

by default.

That’s why we haven’t defined that the “ingredients” field will contain multiple

values; we simply specify an array of values when indexing documents.

Imagine that we have a blog full of food recipes, and we want users to be able to search through them.

Suppose that a user searches for “spaghetti,” in which case we want to search for that value

in both the recipe description, but also within the ingredients.

We can do that just fine with the two text fields.

“text” fields are used for performing full-text searches that do not require exact

matches, so this data type is often used together with raw user input.

Suppose that besides providing search functionality to end users, we want an index page that lists

all ingredients and how many recipes use them.

That’s a job for aggregations, which we will look at later in the course.

For reasons that I won’t get into right now, we cannot run aggregations on “text” fields.

Instead, we need to use the “keyword” data type.

To be clear, aggregations can also be run on dates and numbers, but that won’t help

us in this example.

The point of the matter is that to run aggregations on text values, we need to use the “keyword”

data type.

Since we also need the “text” data type, we basically need two mappings for the “ingredients” field.

You probably guessed it - we can add another mapping to the field.

We do that by specifying a “fields” mapping parameter containing other mappings for a field.

Let’s type that out before going into more detail.

Alright, so what I just did was to add an additional mapping to the “ingredients”

field named “keyword.”

At first it might seem confusing that both the name and data type is set to “keyword.”

This is just a matter of convention; you can name the mapping anything you want, but I

recommend that you name it “keyword” as in this case.

You will see how this makes sense in a moment when I show you how to query this particular

mapping.

First, let’s index a document.

To save some typing, I have prepared the query in advance, since it contains nothing that

you haven’t seen before.

Notice how there is no sign of the “keyword” mapping when indexing documents; we simply

specify the field in the same way as we would if there was no additional field mapping.

Let’s run the query.

Okay, so what actually happened behind the scenes when indexing the document?

As you know, “text” fields are analyzed, so an inverted index was populated with the

terms that were emitted by the analyzer.

That was done for each “text” field.

Nothing new here.

However, since we have an additional mapping for the “ingredients” field, another inverted

index was created for the field, this time using the “keyword” analyzer.

It therefore contains the unmodified values, meaning that the terms have not been lowercased.

This inverted index is optimized for exact matches, and we can use it for aggregations

and sorting as well.

Had we made use of stemming, for instance, the differences between the two mappings would

be more apparent, but I am sure you get the idea.

If we run a “match_all” search query, we will see that the “ingredients” field

within the results looks exactly as normal.

We can query the field exactly as normal with a “match” query, which is the main query

used for full-text searches.

To search for exact values, we need to use something called a term level query.

Don’t worry about these queries for now, as we will get back to all of them later in the course.

All you need to know is that the “term” query requires an exact match for the target field.

When doing exact matches on text values, we should query a “keyword” mapping.

We will take a closer look at why that is the case a bit later in the course.

To query the “keyword” mapping, we specify the field name, a dot, followed by the name

of the mapping — “keyword” in this case.

Unlike the query we just ran, this one will query the inverted index containing the raw

string values supplied at index time - i.e. values that were not analyzed.

Running the query, we can see that it works as well.

That’s how you can define multiple mappings for a field.

This is useful in situations where you need to query a field in different ways.

In this lecture we saw that with a “text” field that also contained a “keyword” mapping.

This allows us to use the “text” mapping for full-text searches and the “keyword”

mapping for aggregations and sorting.

This is probably the most common use case of multi-fields, but it is definitely not

the only one.

You have much more control of how values are indexed than just the data type, because you

can change mapping parameters as well.

An example could be to configure synonyms or stemming in different ways for a single field.

Or having an additional mapping for the “description” field that is optimized for auto-completion

and “search-as-you-type” functionality.

Anyway, the use cases for multi-fields are many, so this is an incredibly powerful feature.

Lastly, I will just get rid of this temporary index since we don’t need it anymore.

I will see you in the next lecture!