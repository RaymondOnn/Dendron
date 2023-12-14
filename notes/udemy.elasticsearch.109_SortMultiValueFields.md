---
id: ffyu6rxg8b0j1b5oeizsto1
title: 109_SortMultiValueFields
desc: ''
updated: 1701194881515
created: 1701192928123
---
Instructor: Now that you know the basics

of sorting results in Elasticsearch, let's look

at something really cool, sorting by multi-value fields.

What this means is that we can sort

by fields containing more than one value

such as the ratings field that we have

for documents start within the recipe index.

Remember that fields may contain more

than one value by default.

So we didn't define this in any mapping.

This particular field is

of the type float and contains an array of ratings.

To sort by multi-value fields,

we need to specify a sorting mode

which can be one of five values.

Let's take a look at one of them

and I'll tell you about the remaining ones afterwards.

So I have a simple query loaded up

ready for us to apply a sort order to it.

In this example, I'll sort by the recipes

with the highest average rating.

So I can do this

by first adding an object here for the sort order.

I want to sort on the ratings fields.

So I'll define an object for this field.

The order will be descending,

i.e. have the highest ratings first.

And then I can specify a mode.

So here I have a few options, average, max, min, and sum.

You'll see all of these later

when we get to talking about aggregations.

For now, I'm interested in the average rating.

So I'll specify avg.

So what Elasticsearch does for us is

to calculate the average rating based

on the numbers within the array.

This is actually known as an aggregation

which you may know from relational databases.

We haven't gotten into talking

about aggregations in Elasticsearch yet

but we'll get to that in the next section.

So what we're doing here is actually kind of a shortcut

to aggregations.

Besides the avg mode,

you saw a couple of other options.

So we also have the sum mode available to us,

which simply sums up the numbers.

There's also a median mode.

These three modes only apply

to fields containing numeric values.

There are two more modes named min and max,

which are not limited to numeric data types.

These modes use the lowest

and highest value for sorting respectively.

Like I said, these two modes are not limited to numbers

so they work with dates as well.

That's it for sorting.

Let's now move on to talking about something else.

