---
id: 43mza6z5xgnf8zmnfcncfza
title: 115_NestedAggregations
desc: ''
updated: 1701195035900
created: 1701193048294
---
-: I mentioned earlier that aggregations in Elasticsearch

are extremely powerful

and that you can do super cool things with them.

It's time to unveil the mystery

and show you some of the tricks that Elasticsearch has

up its sleeves.

You just saw the terms aggregation

which is a bucket aggregation.

As you know, bucket aggregations place documents

within buckets, which are sets of documents.

This is not so useful in itself other than for retrieving

the document counts for groups of documents.

However, what's cool about bucket aggregations

is that they can have nested aggregations

also referred to as sub-aggregations.

You can do this by using the X

or aggregations key exactly as we've done before.

So this is a recursive kind of thing.

This is unlike metric aggregations

that just produce a value.

But since bucket aggregations produce buckets of documents

we can use those buckets for other aggregations.

We can even nest bucket aggregations

within bucket aggregations.

But just to keep things simple at first

I'll nest a metric aggregation

within the terms bucket aggregation.

I hope that makes sense.

What I'll do is to nest the stats aggregation

although I could just as well have used the sum

or avg aggregations, for instance.

Before explaining the details,

let me just show you how it works.

So I've prepared a query in advance

which just includes the terms aggregation

on the status fields, exactly as you've seen before.

Now I'll add a key named X

within the existing aggregations objects.

This key is going to contain an object

of the exact same structure as any other aggregation.

So in that regard, there's nothing new here.

So let's go ahead and add a new key named X

within this aggregation object that we had already

and give the sub-aggregation

or rather the bucket aggregation a name.

I'll name it status_stats

because I want to use the stats aggregation.

So that's also the aggregation type that I'll enter here.

Stats.

And the field will be total_amount.

So basically, this new aggregation that I just added

runs in the context of the bucket

that the parent aggregation created.

Let's run the query and see what the results look like.

We see the same results as in the previous lectures

but apart from that, an object named after our

nested aggregation has been added within each bucket.

These objects contain the output of the nested aggregation.

In this case, we used the multi value metric aggregation

but had we just used the sum aggregation, for instance,

we would just see a number here.

So the takeaway here

is that the statistics that you see here

are specific to the bucket that they appear within.

This means that we now get the sum error, et cetera

for each status.

So we can see that there are

209 orders with a status of processed

with a total_amount field between 10.27 and 281.77.

When we previously used the stat segregation,

the statistics were for all of the documents

matching the query,

which was implicitly the match_all query.

This time around, the statistics

are now specific to a given term.

The way this works is that aggregations are run

based on the context that they're in.

In this particular example,

the terms aggregation is a top level aggregation

so it runs in the context of the query parameter.

In this case, we haven't explicitly added a query,

so Elasticsearch implicitly uses a match_all query.

Suppose that we had specified a query matching only orders

totaling more than 100.

In that case, the terms aggregation would run

in the context of the matches that this query produces.

If we were to do that, we would see the document counts drop

within the aggregations.

Let's quickly do that

just to prove that I'm not filling you with lies.

Okay, so I'll add a query parameter

and arrange query for the total amount fields

saying that the value should be

greater than or equal to 100.

And then I'll run the query.

And sure enough, now we see the document counts drop

because the query now only matches 489 documents

instead of 1000.

So the terms aggregation runs in the context of the query.

While the stats aggregation runs in the context

of its parent aggregation, being a bucket aggregation.

This means that it operates

on the buckets produced by that aggregation.

I hope that makes sense.

So to recap, metric aggregations produce simple results

and cannot contain sub-aggregations.

Bucket aggregations, on the other hand,

may contain sub aggregations

which then operate on the buckets

produced by the parent bucket aggregation.

Aggregations run based on the context

in which they are defined.

Aggregations added at the top level

of the root aggregation object

run in the context of the request query

and sub aggregations

run in the context of their parent aggregation.

You can nest both metric and bucket aggregations

within bucket aggregations

so both aggregation groups can be combined.

You can kind of think of metric aggregations

as being equivalent to leaf level queries

and bucket aggregations being equivalent

of compound queries.

That's because bucket aggregations can contain

other bucket aggregations or metric aggregations

in the same way that compound queries can contain

other compound queries or leaf level queries.

In this lecture, you didn't see any new aggregation types

but now it's time to continue

with some more bucket aggregations.

I wanted to show you how to nest aggregations now

because we'll be using this in the next lectures.

