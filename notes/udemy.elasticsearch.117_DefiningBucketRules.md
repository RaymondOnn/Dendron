---
id: s04b0g1ktc1ndaasg3apbf4
title: 117_DefiningBucketRules
desc: ''
updated: 1701195073929
created: 1701193093133
---
Instructor: You just saw the filter aggregation

and now you'll see a filters aggregation,

which is kind of related.

What you can do with this aggregation is to define rules

for which buckets documents should be placed into.

This is done

by specifying bucket names and the query clauses.

The documents must match to be placed in the buckets

so we could specify a bucket with the name

of shirt and define that only products whose name

contain the term shirt should be placed within that bucket.

Let's go through an example of this

for which I'll use the recipe index again.

Specifically, I want to have two buckets.

One for pasta recipes and one for spaghetti recipes.

So I'll add an aggregation, I'll just call it my filter.

And the aggregation type is going to be filters in plural.

Now, this aggregation contains a filters parameter

which might look strange to you.

It did to me at first as well.

The reason why this is the case is

because you can add parameters to the filters aggregation

so you just have to define the filters

within the filters parameter nested

within the filters aggregation.

I know that sounds confusing, but nevertheless,

that's how this aggregation has been designed.

So let's go ahead and add our first query

or rather our first bucket rule.

I'll name this pasta, which will be in the name

of the buckets that will be created.

This will be an object.

And in here I'll add a query clause.

In this example, I'll use a match query.

So this can be any query clause that you've seen before.

When performing normal search queries,

there's no difference.

So what I want to do is to search the title fields

for the term pasta.

So what this will do is to take any documents

whose title contains the term pasta

and place them within a bucket named pasta.

All right, for this to make sense,

let's add another bucket rule.

I'll give it the name of spaghetti.

Also use a match query.

Again, search the title fields

and search for the term spaghetti.

Okay, hopefully this syntax

doesn't look too confusing to you.

But anyways, let's run the query

and see what the results look like.

So this creates two buckets for us

with the appropriate recipes

and we can see the document counts within the results.

However, you will often want to do something

with the buckets that are created

other than calculating the document counts.

We can add a sub aggregation to the filter

so that the sub aggregation does its work based

on the buckets that the filter created.

Let's calculate the average ratings for each

of the buckets by using the avg aggregation.

Alright, so at the same level as the filters key,

let's define an aggs key.

And in here I'll add an aggregation named avg_rating.

The type will be avg.

And the field will be ratings.

Let's go ahead and run it.

And now, along with the document counts,

we now also get the errors rating for the documents

within the two buckets,

and that's how you can narrow down the documents

before running an aggregation.

Pretty cool, right?

