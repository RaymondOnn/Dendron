---
id: 22xfy6q6qr059ksuo3j86b2
title: 111_IntroAggregations
desc: ''
updated: 1701194924847
created: 1701192968498
---
Lecturer: In this section of the course,

we're going to look at something called aggregations.

You might know what this is all about

from relational databases, but aggregations

in Elasticsearch are much more powerful than that.

So aggregations are a way of grouping

and extracting statistics and summaries from your data.

And aggregation works on a set of documents

which is defined by the execution context.

I know that sounds fancy and all, but you'll see what I mean

in the upcoming lectures, so don't worry about it for now.

Suppose that we have an index where each document

corresponds to an order and each order contains

an amount and a product ID.

A simple example of what you can do with aggregations

is then to group orders by product ID and then

sum the amounts to see how much each product sold for.

Similarly, we could calculate the average order amount

for each product and see which products

generate the most revenue per order.

You can do much more complex things than that

with aggregations, which you'll see throughout this section.

During this section, I'll make use of some new test data

which is more appropriate for working with aggregations.

Specifically, a number of orders start

within an index named order.

You can find this test data attached

to this lecture or within the GitHub repository.

Also, be sure to check the GitHub repository

because I've added mappings there

that you should add before importing the test data.

I have already imported this data in advance

so let's take a moment to walk through

the mapping so you know which fields

are at our disposal for the upcoming lectures.

I've already typed out the query to see the mapping

because you've seen it a couple of times by now.

So let's just go ahead and run it.

So we have a lines field, which is of the type nested

because it contains objects that we need

to be able to distinguish from each other.

That's because the test data includes

objects within an array.

Each order line contains an amount,

a product ID, and the quantity.

We also have a timestamp of when the order was placed

which is stored within the purchased_at field.

Next, we have a field for storing where the order

originated from, i.e., the sales_channel.

Each order has a salesman object consisting of

both the ID and the name of the person who made the sale.

Last but not least, we have a status field

and a convenient field named total_amount,

which is just a sum of all of the amounts

stored within the order lines.

Those are the fields that we'll be using

in the upcoming lectures.

Now, there are a number of different groups of aggregations

which we'll look at separately

beginning with so-called metric aggregations.

So let's get started.