---
id: y1ndq2k69j88tgdppixhe99
title: 113_BucketAggregation
desc: ''
updated: 1701194975011
created: 1701193007129
---
Instructor: Having looked at metric aggregations,

it's time to look at the different

group of aggregations

namely bucket aggregations.

Bucket aggregations are a bit more complicated

than metric aggregations,

but are extremely powerful

and enable you to do really cool things

which you'll especially see

in a couple of lectures.

Instead of calculating metrics for fields,

bucket aggregations create buckets of documents

so basically sets of documents.

Some aggregations may yield a single bucket,

others a fixed number,

and some create multiple buckets dynamically.

Each bucket has a criterion

which determines whether or not a given document

will fall into that bucket.

I realize that sounds a bit complicated

so let's fast forward

and get to some examples

that make everything easier to understand.

Just note that instead of calculating values,

also known as metrics,

bucket aggregations create buckets

which are basically just sets of documents.

The first aggregation I'll make use of

is the terms aggregation

which dynamically builds a bucket

for each unique value.

We can use this to group orders together

based on the value of the status fields.

So let's do that.

So I have the skeleton for query loaded up here

and I'll add an aggregation named status

underscore terms

being an object as you've seen before.

And the aggregation type is terms.

Let's just get rid of the size parameter for now

and specify the fields to be status

and go ahead and run it.

Inspecting the results,

we can see that a bucket has been created

for each unique value of the status field

under our buckets key.

The values are returned under the key "key"

and the doc underscore count key

specifies how many documents

fell into each bucket.

Notice how the response also includes two keys

named doc count error upper bound

and sum other doc count.

I won't discuss the first one now

because I'll get back to that one

in more details in the next lecture.

So what's the sum other doc count key all about?

Elasticsearch returns the top unique terms.

So if you have many different terms

then some of them will not appear in the results.

The sum other doc counts key

is the sum of the document counts

for the terms that were not part of the response.

In this example,

the number is zero

because we only have five different values

for the status field.

Suppose that we have 50, for example.

In that case, we would not see all

of the terms appearing as a bucket.

The sum other doc count key

would then contain the sum of the doc

underscore count keys

for the buckets that were left out of the response.

Let's see what happens

if I change the field to the total

underscore amount fields

which has many different values

and run the crew again.

Now the sum underscore order underscore doc

underscore count key

has the value of 986.

If we sum all of the doc underscore count keys

in the response together,

we would get 14

since we have 1,000 documents.

I'll just revert this change

and change the field back to status.

Since we only have five unique values

for the status field

we won't see any difference,

but I'm sure you get the points.

But what if we have documents that don't

contain the status field at all

or have a value of null?

By adding a missing parameter,

we can specify the name

of a bucket in which such documents should be placed.

So let's add this parameter named missing

and the value will be the name of the bucket.

I'll just add it to N slash A,

short for not available,

and running this query

we don't actually see any difference.

That's because there are no orders

that either don't have the status field

or container value of null.

We might still want the bucket to be returned though.

Perhaps though some application code

doesn't need to conditionally handle

if it's present or not.

We can do that by adding a parameter

named min underscore doc underscore count.

Which specifies the minimum number of documents

a bucket needs to contain

to be present within the results.

The default value is one

which is why the missing parameter

did not yield any buckets

within the results.

Let's set the parameter to zero

and we should see the bucket appear.

So min

underscore doc

underscore count

and set it to zero

and then run the query again.

And sure enough,

now we see a bucket named N slash A

within the results

with a document count of zero.

Now I just want to briefly mention

that the calculated document counts

are approximate

so in some scenarios this may not be accurate.

I'll get back to that in much more detail

in the next lecture,

so I won't talk more about it now.

It's also possible to order the buckets

in various ways

including by sub aggregations

which is something that we haven't looked at yet.

So instead of that,

I'll just order the buckets

by their terms

in a standing order.

We can do that by adding an order parameter

containing the underscore term key

and the order direction.

The underscore term key

is a special key that allows you

to refer to the buckets key

being the value of the status field.

So let's see that in action.

So an order parameter,

an underscore term,

and a standing order,

and let's see what it looks like.

And now we can see that the buckets

have been ordered according to the status field

in a standing order.

Alright, that was actually

the only aggregation that I wanted to cover

in this lecture

since the terms aggregation

is a good introduction

to bucket aggregations.

In the following lectures,

we're going to go through

some more bucket aggregations

now that you know the basics

of how they work.

So just to recap on the main takeaways

from this lecture.

Bucket aggregations create buckets

which are sets of documents

based on some criterion.

The criterion depends on the particular bucket aggregation

that's used.

In this lecture,

you saw the terms aggregation in action

which creates a bucket for each unique term

for a given field.

Bucket aggregations enable us to do a lot

of cool things which you're going to see soon.

