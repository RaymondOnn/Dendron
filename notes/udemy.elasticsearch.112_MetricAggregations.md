---
id: ibvl9szp7gr1z8ooxx845oq
title: 112_MetricAggregations
desc: ''
updated: 1701194953893
created: 1701192987395
---
Instructor: Now we're ready to begin

writing our first aggregations.

We'll begin by looking at a group

of aggregations referred to as metric aggregations

because these are the simplest ones.

In fact, you are probably going to recognize most

of them from relational databases

if you are familiar with those.

This group of aggregations is divided

into two subgroups or categories.

Now, before I tell you what these are called

just take a second to prepare yourself

for some super fancy terminology.

Are you ready?

They're called single-value numeric metric aggregations

and multi-value numeric metric aggregations.

That sounds pretty fancy, right?

Don't worry, it's nowhere near as complex as it sounds.

The single-value aggregations simply output a single value,

which could be the sum of numbers or an average value.

Unsurprisingly, multi-value aggregations

yield multiple values rather than just a single value.

You'll see examples of both kinds

of aggregations in this lecture.

So let's get started.

Let's begin with what is probably the simplest aggregation,

the sum aggregation.

It simply takes a given numeric field

for a number of documents and sums up the numbers.

So which documents are used for the aggregations?

You might wonder.

To start out with, we'll use the results

of a search query defined within the query object.

So any search query that you've seen throughout this course.

That being said, we don't need to explicitly add a query

if we just want to aggregate all documents.

If we wanted to, we could define a match all query

but we can also just leave out the query object altogether,

which I'll do for now.

So for the following queries, we'll be using all

of the documents within the order index

for the aggregations.

If we wanted to only use documents

with a total amount of less than 50

we could add a range query,

in which case the matches for this query

would be used for the aggregations.

Anyways, enough talking.

So let's write a query that includes an aggregation.

So GET and the new order index,

defaults, search API as always.

And first, I'll set the size parameter to zero

because we're only interested in the aggregations

and not the results of the query.

Remember that I don't need to add query objects

because I'm implicitly matching all documents

by leaving it out.

So what I'll do instead is to specify an X object.

This object can be named aggregations as well if you prefer,

but I'll just use X in this course.

Okay?

So what I want to do is to sum up the total amounts.

So what we'll have is the total sales,

and this is just the name of the aggregation.

So that's just an arbitrary name that I define.

So it can be anything you want.

And here we have to enter the aggregation type.

That's going to be sum

because we're going to be summing up numeric values,

and within this object we can configure the sum aggregation

by specifying parameters.

All I need to do in this case is to add a field parameter

containing the name of the field that we want to aggregate.

In this case, that's going to be total amount.

Let's run the query and see what the response looks like.

As you can see, we now have a key named aggregations

containing an object with the aggregations

that we specified within the query.

Also, notice how the sum aggregation is nested

within a field named according to the name

that we specified within the query,

total_sales in this example.

Pretty easy, right?

Let's move on to a query named avg,

short for the word average.

As you can probably imagine

it calculates the average value for a given field.

I'll use the total_amount field again.

We could add a whole new query for this aggregation,

but I want to show you how we can easily add more

than one aggregation to a query.

Besides, I also want the average value

to be for all documents.

So what I'll do is that I'll just add

a new aggregation right here and I'll name it avg_sale.

This will be an object as well.

The type will be avg and the field will be total_amount.

Running the query, we'll see an additional aggregation

alongside the sum aggregation.

But hey, why start with two aggregations

when we can have three?

Let's add two more aggregations of the types min and max.

These aggregations return the minimum

and maximum value for a given field respectively.

So the lowest and highest order amounts.

So what I'll do is that I'll copy this just

to save some typing.

I'll name the first one min_sale

and set it to a type of min, short for minimum.

The field is still going to be total amount.

Then I'll paste in a new aggregation down here

and do the same thing.

So max_sale, a type of max,

and again the total amount field.

And sure enough, we see the lowest and highest order amounts

being returned within the results.

Okay, this is getting out of hand, isn't it?

Four aggregations in one query.

There's nothing wrong with that, and you can add even more

if you wanted to, but let's keep everything nice

and short and write a new query.

I'll just make a copy of the existing query

and empty the aggregations objects.

Let's add a couple of spaces,

and I'll get rid of all of this, like so.

The next aggregation I want to show you

is named cardinality.

What it basically does is that it counts

the number of distinct values for a field.

Let's use this aggregation to find out

how many different salesmen are associated

with an order for which I'll use the ID field

within the salesman object by specifying a field path.

So if that sounded confusing, let's type it out.

So the name of the aggregation

will be total_salesmen being an object as always,

and the aggregation type is going to be cardinality,

and the field will be salesman.id.

So the ID field is what we're aggregating on, and that's it.

Let's run it.

The results show that for all of the orders

within the index, there are 100 different salesmen.

Something important to know

is that the cardinality aggregation

produces approximate numbers.

The explanation for this gets very technical,

but basically it comes down to the fact

that producing exact numbers

would take up too many resources from the cluster.

Therefore, an algorithm is used to try and yield numbers

as accurately as possible.

Elasticsearch does pretty well,

so you will generally see quite accurate results,

but if you need highly accurate numbers

then you should keep this in mind.

You can control the precision

and sacrifice some performance if this is important to you,

in which case I encourage you

to check out the documentation.

Anyways, let's move on to the next aggregation,

which is named value_count.

This aggregation counts the number

of values that the aggregation is based on.

I'll use the total_amount field for aggregation.

So in this example, the number will be 1,000

because we have 1,000 documents within our index,

and the match all query is used implicitly.

But anyways, let's see it in action.

So I'll just make a copy of this query,

paste it in, add some spaces,

and I'll get rid of this

and add a values_count aggregation.

Remember this is the name, not the type.

And here I'll add the type of value_count,

and the fields is going to be total_amount.

Let's run it.

And the results do indeed specify 1,000.

So the value_account aggregation

gives us the number of values

that the aggregation used for doing its work.

This will often be useful in conjunction

with other aggregations.

For instance, if we use the avg aggregation

to calculate an average order amount,

then we might want to use the value_count aggregation

to know how many orders the average value was based on.

This would especially be useful if we were using a query

other than the match all query because the number of matches

would therefore not be as predictable.

Sometimes you might need to use several

of these aggregations at the same time for the same field.

Instead of specifying multiple aggregations,

Elasticsearch provides

a convenient multi-value aggregation named stats.

This aggregation calculates the numbers returned

by the min, max, sum, avg and value count aggregations.

Let's use this aggregation for the total amount field

and see what the results look like.

So again, let's paste in a copy of the query,

get rid of what we had already,

name it amount_stats,

specify the stats type

and the total amount field as always,

and run it.

As you can see within the results,

the stats aggregation returns the same numbers

as you have seen before.

So it's just a convenience aggregation.

And that was the last metric aggregation

that I wanted to show you.

There are a few others,

but those were the most important ones

and the ones that you will typically need.

If you need something very specific

or if you're just curious

then be sure to check out the documentation.

You can find a link attached to this lecture.

