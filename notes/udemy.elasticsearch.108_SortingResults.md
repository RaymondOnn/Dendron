---
id: tavmau7j7it4bj6c8y3pcne
title: 108_SortingResults
desc: ''
updated: 1701194847607
created: 1701192906844
---
Instructor: We have looked at how to write

various queries

and how to deal with pagination,

but we haven't looked at how to sort results.

Well, until now.

Sorting results is quite easy in Elasticsearch

and is pretty much the same deal

as sorting rows with the ORDER BY clause

in relational databases.

So, as you know, I'm a lazy person,

and that includes cooking, too.

Therefore, I want to find the recipes

that take the least time.

So, I want to order results

by the preparation_time_minutes field in ascending order.

I already have a query prepared that matches all documents.

It doesn't return any fields from the _source metafield,

which you will see why in a moment.

First, let's define how the hits should be sorted

within a sort parameter.

The default sort order is ascending.

So, all we need to do is to specify the name of the field

that we want to sort by.

So, let's do that.

I'll do it below the query key here.

So, sort, and as you can see, it should be an array.

Let me just get rid of this, first of all,

because now, I just want to start

with the simplest possible syntax,

being an array of strings.

So, I'll add a string for the

preparation_time_minutes field.

So, I'm just entering the field name here.

And, let's run it.

And, looking at the results,

notice how we have a new key named sort.

This key contains an array of the values

that the results were sorted by.

In this case, that's the values

for the preparation_time_minutes field.

That's why I excluded

the _source metafield from the results,

because we can see the values within this new array.

Next, I want to show you how to specify the sort order

because I want to find the latest recipes,

meaning sorting by the created field in descending order.

So, what I'll do is to make a copy of the existing query,

paste it down here, and then adjust it accordingly.

I'll also update the _source parameter here

to include the created field.

You'll see why in a moment.

And then, let's go down here,

get rid of what we already had within the sort array,

and enter an object instead of a string.

Within this object, I'll add a key

with the name of the field being created

and for the value, I will enter the sort direction

where I can enter asc for ascending

or desc for descending.

Okay, let's run it.

And now, we see the newest recipes first within the results.

The reason I included the created field

within the _source metafield

is because in the case of date fields,

the sort key includes a number.

This number represents the number of milliseconds

since the epoch.

So, basically, a Unix timestamp multiplied by 1,000.

This is the representation that Elasticsearch uses

for dates internally,

and in this case, for sorting.

But, I just wanted to include the date

so it's easier for us to read.

Next, let's try to sort by multiple fields.

As in SQL and many other technologies,

this is indeed possible,

but I probably gave that away already

by specifying an array for the sort parameter.

You don't have to do that, by the way.

If you just want to sort by

a single field in ascending order,

then you can just specify the field name directly

for the sort parameter.

I just personally prefer to use an array.

Anyways, let's make a copy of the query

and sort by another field.

What I'll do is to combine the two fields

that we have sorted by in the first two queries.

So, the idea is to sort by the preparation time

in ascending order

and the created timestamp in descending order,

just to make the results easier to interpret.

I'll add these two fields to the _source metafield.

So, let's do that. That's the first string.

So, I'll get rid of this and add an array here instead,

being an array of strings containing the field names.

So, first preparation_time_minutes, and then created, okay?

And, let's now get to work on the sort parameter down here.

So, since I want to sort by the preparation time

in minutes first,

I have to add an object above what we had already.

So, the object is just going to be of the same structure

and the order is going to be ascending.

Note that you don't have to define this explicitly,

but that's just a preference of mine,

so it's easy to see what's going on when reading the query.

Okay, let's go ahead and run it.

So, we only see the second sort order in effect

when multiple documents have the same preparation time

because those documents will then be sorted

by the created timestamp.

So, if we scroll down a bit,

we'll find a bunch of recipes

with a preparation time of 15 minutes.

Looking at the dates,

we can see that the latest ones appear first.

So, this is essentially the second sort order

that we specified in action.

Also worth noting is that you can use a special field

named _score to sort by score.

Since sorting by score is the default behavior,

this only really makes sense

when specifying a custom sort order.

Those were the basics of sorting in Elasticsearch.

That was pretty easy, right?

But, that's not all we can do with sorting.

So, let's continue and look at what else we have

in our toolbox in regards to sorting.

