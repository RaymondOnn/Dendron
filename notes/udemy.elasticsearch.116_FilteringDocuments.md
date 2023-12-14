---
id: fgi2v13h14okoxsx38jouy0
title: 116_FilteringDocuments
desc: ''
updated: 1701195054472
created: 1701193066972
---
Lecturer: Now that you know what sub-aggregations

are all about, it's time to show you

another bucket aggregation.

As you know, the documents that an aggregation uses

depends on the context in which the aggregation is defined,

but sometimes you might want to filter out documents

for a given aggregation.

Maybe you have some products

and you need to add an aggregation within a context,

where both ebooks and paper books

are included, for instance,

but you're only really interested in paper books.

You can then use a filter to narrow down

the set of documents that an aggregation

will use as its context.

A filter uses a query clause,

exactly as we've done within search queries,

so this could be a term query

or a match query, for instance.

Let's see an example of how we can filter out documents

before running an aggregation.

So, let's add an aggregation.

I'll name it low_value

because I want to filter out orders with a low value

for the total_amount field.

So, I'll add a filter key here with a range query,

so you just add a normal query clause

within this filter key, exactly as within search queries.

The field will be total_amount,

and I'll use the lte key here

with a value of 50, and then I will add a sub-aggregation,

so this low value aggregation.

So, let's do that,

add an aggs key right here,

and name the aggregation avg_amount,

and the type, avg.

The field will be total_amount.

Before explaining this further,

let's just go ahead and run it

so we can see what the results look like.

Whoops, apparently I misspelled this key here,

so it had to be lt,

not te.

Let's run it again.

So, what happens here is that the avg aggregation runs

within the context of the filter,

meaning that it only aggregates the documents

that match the range query.

So, the range query is a top-level aggregation,

meaning that it runs in the context of the query,

which is an implicit match_all query in this case,

and the avg aggregation runs in the context

of the filter aggregation,

meaning that some documents have been filtered out.

So, the bucket that the avg aggregation works upon

doesn't have any others with a value of less than 50.

And that's how you can use a filter to filter out documents

before creating a bucket for another aggregation.

