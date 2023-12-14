---
id: dq29rqqz1t48h7fppllrvh4
title: 110_Filters
desc: ''
updated: 1701194898839
created: 1701192941460
---
Instructor: In this lecture

we are going to talk about filters.

As you hopefully remember,

queries can be run in two contexts.

In a query context and in the filter context.

The difference is that queries run in a query context

affect relevance and filter queries don't.

Apart from documents having to match a query

in the query context, the query also answers the question,

how well do the documents match?

Filters on the other hand, are just yes or no questions.

So filters do not affect relevance scores.

That's why you'll typically use filters for number

or date ranges and term queries matching keyword fields

such as a status field or something like that.

Does this remind you of something?

We actually saw this when working with the bool query.

More specifically, when adding queries

to the must underscore nut objects.

These queries were run in a filter context.

Let's now do the opposite of the queries

within the must underscore nut objects.

Meaning to add queries that documents must match.

I'll actually be using the pool query again

for this purpose.

In previous versions of Elasticsearch,

filters were specified at the top level

of the request body but this is no longer the case.

Instead, a parameter named filter can be added

to various queries, most notably the bool query.

So I have a simple query prepared in advance

which simply matches recipes

with the term pasta within the title field.

I guess you know that I'm lazy by now

so I want to get things done fast.

I'll therefore apply a filter requiring recipes

to have a preparation time of maximum 15 minutes.

I'll do that by adding a range query

within a filter parameter.

So let's see what that looks like.

So within this object at the same level

of the must array here, I'll add a comma

and specify a filter key

and I'll add it as an array for the same reasons

as the other keys within the bool query.

So here I'll add a query clause being an object.

I'll use a range query like I said.

Define that as an object as well.

And then define the fields

which is preparation underscore time, underscore minutes

and this is an array as well.

Nothing new here.

This is just a standard range query.

So LCE 15, meaning less than or equal to 15.

The reason I added this range query

within the filter parameter is

because Elasticsearch wouldn't use the query

for calculating relevance scores anyways,

since it's a yes or no kind of thing.

So the match query affects relevance

and the range query doesn't.

Although I added the query within the filter parameter,

I could just as well have added it

to the must underscore not parameter with a small change.

In that case, I could just change the range query

to match recipes with a preparation time of more than 15.

This is the same thing because query clauses

within both of these parameters are run in a filter context.

And that's how to add a filter.

So why didn't I mention that when talking

about the bool query?

Because filters are not specific to the bool query

even though I use that query in this example.

You can also use filters with aggregations

and other queries that we're going to look at later.

That being said,

you will typically add filters to bool queries

because that's a very flexible and common query to use.

As a last thing, just a reminder of why you would want

to use filters in the first place.

I already talked about how filters don't affect relevance

so there's that, but filters are also more efficient.

That's because Elasticsearch will automatically

cache filters that are used frequently

and we also save a bit of computation time

when not having to calculate relevance scores.

