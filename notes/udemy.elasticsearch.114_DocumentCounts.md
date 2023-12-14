---
id: we9exebvcx2juq4iduqmzcc
title: 114_DocumentCounts
desc: ''
updated: 1701195000450
created: 1701193027065
---
Instructor: So you just saw the term segregation,

which groups documents into buckets by unique terms.

Something you should know about the terms query

is that the document counts are approximate

and not guaranteed to be accurate.

If knowing that is sufficient for you

and you don't care about the technical details,

then you can go ahead and skip this lecture.

Otherwise, I want to take a moment

to explain why this is the case.

The reason why counts are not always accurate

is because of the distributed nature

of an Elasticsearch cluster.

Specifically, the fact that an index

is distributed across multiple shards, at least by default.

The way the terms segregation works

is that the coordinating note that is responsible

for handling the search request

prompts each shard for its top unique terms.

So if we specify a size parameter of three,

each of the index's shards is asked

to provide a top three of its unique terms.

That's because an index's data

is split across multiple shards,

so there's no single source to retrieve the results from.

The coordinating note then takes the results

from each of the shards

and uses those to compute the final result.

This last step of the process

is where the counts can become slightly inaccurate,

so let's walk through an example of that.

Suppose that we have a number of orders

stored within three shards, A, B, and C.

Each order has a product,

so suppose that we aggregate on the product name.

You can see the top five for the shards

in the following table,

with the document counts in parentheses.

Each shards will then take the three products

with the highest document count, i.e., the number of orders

that contain the given product name,

and send this to the coordinating note.

This note then takes the results from each of the shards

and puts it together by aggregating

the number of documents and sorts by those,

provided that no custom sort order is specified.

If we sum up the results from each of the shards,

we'll get the following results.

150 orders are for product A, 80 orders are for product B,

and 60 are for product F.

This is the result of merging the results from the shards,

but here comes the moral of the story.

This is not the correct results.

Let's see why.

Looking at the first table, we can see that product A

is in the top three for all of the shards,

meaning that the number of documents

for this product is correct.

Each shard reported how many documents it contains

with this particular product,

so there's really no doubt about that at all.

But let's take a look at product B for a moment.

This product is part of the top three

for shards A and B, but not for shard C.

For shard C, we can see the product B

is in the fourth position.

The same concept applies for product F.

It's part of the top three for shards B and C,

but not for shard A.

Great, so what does this mean?

It means that the document count

for both of these products are wrong

because the coordinating note only aggregates

the counts it received from each of the shards.

Therefore, it has no idea that product B and product F

each appear 20 times more than specified

within the results that it received from the shards.

This means that the document counts

for product B and product F are each off by 20.

If we factor the missing counts in,

we still get the same order,

but that might not always be the case.

Either way, we do get slightly misleading counts.

This got a bit technical,

but I hope that you understand what happened.

This is just an example

of how the document counts can be misleading.

This is especially a problem

if the size parameter is set to a low number.

The accuracy of the document counts increases

the higher the size parameter is,

but this also makes the queries slower,

so it's really a trade-off between accuracy and performance.

I do want to mention, though, that the default size is 10,

which means that what I just showed you

is more unlikely to happen

than when just retrieving the top three terms.

This also means that if you're only interested

in the top three, you might want to leave the size at 10,

because this will result in a higher accuracy.

If you don't need high accuracy

and want to improve the performance,

then you can, of course, reduce the size,

but I do recommend keeping it

at a minimum of 10 for the most part.

Now, all of this is only a problem for instances

consisting of more than a single shard.

If all of an index's data is stored on a single shard,

then the numbers will be accurate.

That's something you can tweak

if all of an index's data fits on a single shard,

but usually you'll have more than one shard per index,

especially since that's the case by default.

All right, so just one more thing.

Remember the key name doc_count_error_upper_bound

that was part of the results in the previous lecture?

What I just explained in this lecture

enables me to explain what this key is all about.

So the key contains a number

representing the maximum possible document count

for a term that was not part of the final results.

That probably sounds cryptic,

so consider the first table for a moment,

specifically the third row.

What Elasticsearch does is that it takes the document counts

from the last term that was returned from each shard

and sums them up.

In this example, that summation would yield 90,

which would then be returned

for the doc_count_error_upper_bound key within the results.

Taking a look at the second table,

this means that in the worst possible scenario,

a term that was not part of the results

could have a document count of 90

and actually have the second highest document count.

Remember that the problem is amplified by the fact

that only a size of three is used in this example,

but that's just to show you the potential problem.

So what is this number even good for?

Well, most people probably won't make use of it,

but it does provide you with an error margin,

which you can do with what you want.

In this lecture, we took a closer look

at how how the document counts can be inaccurate.

In most scenarios, this will not be a problem,

but I wanted you to know what happens behind the scenes.

More often than not,

you should keep the size parameter a minimum value of 10,

even if you only need the top three terms, for example,

because the higher the value, the more accurate the results.

Now that you've got some insights into the terms query,

let's continue our way through the bucket aggregations.

