---
id: gylhwzayvnbc0ql9odbqvv0
title: 119_Histograms
desc: ''
updated: 1701195106630
created: 1701193123247
---
Instructor: We just looked at range aggregations

which enabled us to specify a number of ranges

determining which bucket the documents should be put into.

But when doing that,

we explicitly had to define the ranges ahead of time.

What if we wanted to specify intervals of 25

for the order amounts?

That requires us to know the minimum and maximum amounts,

unless we can accept missing out on some orders,

which we probably can't.

And even so, we would have to add a lot of ranges

explicitly.

Sure, we could automate this within the application

that sends the query to Elasticsearch,

but fortunately there's a better way,

using something called histograms.

A histogram dynamically builds buckets

from a numeric field's value based on a specified interval.

Take our total_amount field, for example.

We might want to know the distribution of order amounts.

By using a histogram aggregation,

we can specify an interval of 25, for example,

in which case Elasticsearch will dynamically make buckets

for each step of the interval

between the minimum and maximum value for the field.

I apologize if that sounded confusing.

So let's take an example.

Suppose that we have orders with amounts between 0 and 100,

and let's assume that we have a document

with each of the possible values.

With the interval set to 25,

Elasticsearch will create five buckets for us,

buckets with the keys 0, 25, 50, 75, and 100.

These buckets will then be filled with documents.

But what determines which bucket the document falls into?

The total_amount field is evaluated for each document

and the value will be rounded down to the closest buckets.

So if we have a document with an amount of 60,

this number will be rounded down to 50

and placed in the bucket with the key of 50.

Hopefully that makes sense,

but let's write the example that I just mentioned

because that makes it much easier

to understand what histograms are all about.

So let's add an aggregation named amount_distribution

and set the type to histogram.

The field will be total_amount and the interval will be 25.

Let's go ahead and run it.

Looking at the results,

notice how we have buckets following an interval of 25.

The buckets start at zero and go all the way up to 275.

The buckets stop there because the maximum amount is 281

and that document then falls into the bucket

with the key of 275

because the value is rounded down to the nearest buckets.

Now, by default, buckets will be created

for each interval between the minimum and maximum values

for the fields,

regardless of whether or not any documents

fall into the buckets.

That's why you might potentially see buckets

with a document count of zero.

We don't see that within our results

because the values within our test data

are pretty evenly distributed,

and we haven't filtered the documents in any way.

But if this were not the case,

then you might experience empty buckets.

If you don't want that,

then there's a convenient parameter named min_doc_count.

This parameter simply lets us configure how many documents

must fall into a bucket for it to be included

within the results.

This means that we can simply specify a value of one

to ensure that no empty buckets are returned

within the results,

if that's what you want, of course.

So let's try to do that.

So min_doc_count, and I'll set it to one.

Like I said, this doesn't make any difference

with the current query,

but now I want to show you something else.

For that, I need to filter the documents a bit.

So I'll just add a query that only matches orders

with a total_amount field of at least 100.

So I'll do that above the aggregation right here.

So query and a range query, the total_amount field,

and greater than or equal to 100.

Okay, let's run it.

And now we can see that the first bucket has a key of 100.

So we just removed a handful of buckets.

Those buckets are not part of the results anymore

because the histogram aggregation

has no idea about any documents with a lower value than 100

for the total_amount field.

The aggregation includes buckets

based on the documents that are present

within the context that the aggregation is run within.

But what if you still wanted the buckets

below 100 to be part of the results

even if they contain no documents,

or perhaps you don't know if the buckets

will contain any documents based on the aggregation context

because you probably don't know which documents

are matched ahead of time?

To force buckets to be present within two boundaries,

we can use the parameter named extended_bounds.

This parameter should contain an object

with the min and max keys.

Let's add that object and specify 0 and 500

as the minimum and maximum values respectively.

So let's go down here and add another parameter

to the histogram object.

So extended_bounds being an object, like I said,

with a min key, I'll add zero,

and max with a value of 500.

Let's run it.

This means that buckets will be created between 0 and 500

according to the specified interval,

regardless of whether or not any documents

fall into those buckets.

Compared to the min_doc_count parameter,

the extended_bounds parameter

is a way to extend the boundaries of the buckets,

or at least force the configured boundaries.

Let's take a look at the results.

We don't really see any difference.

That's because we have the min_doc_count parameter

set to one

causing the buckets to be filtered out.

Let's try to reset this parameter to zero,

in which case we should see the buckets appear.

And let's run it again.

And sure enough, we now see a number of buckets

with values lower than 100

but also with values higher than the highest amount,

which is 281.

Very similar to the histogram aggregation,

there's a date_histogram aggregation.

It's probably no surprise to you

that it does the same thing, just for date values.

The functionality is almost the same,

with the main difference being the use of an expression

for the interval parameter.

Specifically, we can choose one of the following values:

year, quarter, month, week, day, hour, minute or second.

Let's write an example date_histogram

that shows the number of orders for each month.

So I'll just make a copy of the existing query.

Right, let's get rid of the query

and just keep the aggregation.

I'll name it orders_over_time

and use the date_histogram type.

I'll just get rid of these parameters right here

and just keep the field and interval ones.

So the field will be purchased_at,

and the interval will be month.

The same rules apply

for determining which bucket a document belongs to

as with the histogram aggregation.

Scrolling through the results,

we can see that we now have a bucket for each month.

If you want or need to, you can customize the key format

by specifying a date format for a parameter named format.

Last but not least, I want to mention

that in case you need to,

both the histogram and date_histogram aggregations

support an offset parameter.

This offset is useful

if you want to add or subtract from each bucket key.

For the histogram aggregation, you would specify a number,

and for the date_histogram aggregation,

you should specify a date math expression

such as 1t for instance.

