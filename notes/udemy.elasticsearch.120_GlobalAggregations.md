---
id: cbmkj0clx3w9cv69zi90am9
title: 120_GlobalAggregations
desc: ''
updated: 1701195124518
created: 1701193138835
---
Instructor: Earlier you saw how to filter out documents,

but in this lecture we are kind of going to do the opposite.

By using an aggregation named global,

we can kind of break out of the aggregation context,

so to speak.

What this means is that even

if we have included a query

which narrows down the set of documents

that an aggregation would normally use,

we can get access to all documents

as if the query was not there.

Let's see an example of that.

So I have a simple query loaded up

which narrows down the set of documents

that we would normally aggregate.

So the query matches documents

with a total amount of at least 100.

Let's add a global aggregation

to break out of this context

and aggregate all documents instead.

So let's add an X key here

with an all_orders aggregation with a type of global.

So this aggregation means

that we're essentially accessing all documents

and not just the documents that have a total amount

of at least 100.

So this creates a bucket,

so let's do something with that bucket

by adding a sub aggregation, I'll call it stats_amounts.

And use the stats type with the field of total_amounts.

Okay, running in the query,

notice how the document count for the aggregation is 1000,

and not the 489 documents that the query matched.

That's because the global aggregation is not influenced

by the search query.

Instead, it uses the context of the index

and type that the query is run against and uses all

of the documents that are available within this context.

So implicitly it'll match all query.

All right, let's take a moment to add another aggregation,

one that's not global.

So I'll do that at the same level

as the all_orders aggregation

and I'll name it stats_expensive,

like so,

and use the stats aggregation also

for the total_amount field.

Let's go ahead and run it.

So running this query,

notice how the aggregation uses 489 documents

for the aggregation.

These are the documents that were matched by the query.

Since the new aggregation is not a global aggregation,

it uses the context of the search query,

which has narrowed the documents down

to 489 documents with a total amount of at least 100.

And that's simply

because we didn't use the global aggregation.

This is also apparent if we look at the statistics

that the aggregation has calculated,

because we'll see that the amounts are higher.

And that's how to aggregate on a global context,

even if a query has narrowed down a set of documents

for the aggregation context.

One thing to note is

that global aggregations can only be placed

at the top level of the aggregations objects.

That's not a problem though,

because it wouldn't make any sense

to place a global aggregation as a sub aggregation anyways.

