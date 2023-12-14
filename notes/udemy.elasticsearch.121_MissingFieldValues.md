---
id: 66baahsqgnsqhtzq222kf49
title: 121_MissingFieldValues
desc: ''
updated: 1701195143033
created: 1701193154055
---
Instructor: So far, we have just assumed

the documents contain the field

that we are using within aggregations.

But what if this is not the case?

What if we're aggregating on a status field

and we have some documents

that do not have this field?

Or what if the value is null?

We can aggregate these documents

within aggregation named missing.

Before showing you what the aggregation looks like

we need a couple of documents

that either don't contain the status field

or contain null as the value.

I've prepared two super simple queries

for adding two test documents

so let's just run those first of all.

All right, now that we have some test data

I can show you what the aggregation looks like.

So let's add an aggs key

and name the aggregation orders without status

and write missing as the aggregation type

and the field will be status.

And that's it, let's run it.

Since the missing aggregation is a bucket aggregation

it creates a bucket with the orders

that either don't have a status field at all

or have a value of null.

Looking at the results, we can see

that this is only the case for two documents,

the ones we just added.

That's really all there is to the missing aggregation.

I do want to mention

that it respects the null_value mapping parameter

in case you use that in the field mapping.

You usually use the missing aggregation

in combination with other aggregations.

For example, we might use the sum metric aggregation

to aggregate the total amounts

for the orders without the status.

So as you know, we can easily do this

by adding a sub aggregation.

I'll name it missing_sum

and just specify sum here

and total_amount as the fields and run it.

And as we can see, the total amounts

for the orders without the status fields is 300.

Of course, you don't necessarily need to combine

the missing aggregation with other aggregations.

It can be useful in itself

to find invalid orders or something like that.

Either way, before wrapping this lecture up

I just want to remove the two test orders.

So I'll just write DELETE /order/default/1001,

make a copy and specify 1002

and run these two queries.

