---
id: pj4wxu2g93b31rtab93exkp
title: 118_RangeAggregations
desc: ''
updated: 1701195088172
created: 1701193109574
---
Lecturer: Another way of specifying

which documents should be placed within a bucket

is with range aggregations.

There are two range aggregations,

one named range and one named date_range.

They're basically the same except that

the date range aggregation is dedicated

to working with date values.

They both work in the way that you define ranges,

where each range represents a bucket of documents.

We then specify which filters should be used

for placing documents within the appropriate bucket.

Let's begin with the range aggregation, which is similar

to the range query that you saw earlier in the course.

Perhaps we want to know how

the order amounts are distributed.

More specifically, we want to know

how many orders are between zero and 50,

between 50 and 100, and how many are more than 100.

Let's write a range aggregation that does that.

So I'll give it a name of amount_distribution

and set the type to range.

The field will be total_amount.

And within the ranges parameter, we can define ranges

as objects because this parameter contains an array.

The first range I'm interested in

is the one from zero to 50.

So I can just go ahead and remove the from key

within this object and change the to key to 50.

Note that the from key is included

and the to key is excluded.

Okay, so let's add another range.

Bring an object, set the from key to 50

and the to key to 100.

And the last range will just contain

a from key with a value of 100.

So any document with a value of more than 100

for the total_amount fields will be placed in this bucket.

Okay, let's run this query.

So the results contain a bucket

for each of the ranges that we specified.

Each bucket contains the document count

as with any other bucket aggregation,

but it also contains the from and to values

used for placing documents within that bucket.

Lastly, a key is included, which is just

a textual representation of the range by default.

We can change this, which I will show you

after looking at the date range aggregation.

So the date_range aggregation is conceptually

the same thing, except that it works with dates.

The aggregation therefore enables us to do

date specific things such as using date math

as you saw earlier in the course.

That works in exactly the same way as what you saw earlier.

We can also just provide plain dates if we prefer,

but let's use a bit of date math

just to make things more interesting.

What I want to do is to create two buckets,

one for the first half of 2016

and one for the second half of the year.

For that, I'll be making use of the purchased_at field.

So I'll just make a copy of this existing query

and get rid of these ranges that we had already.

Then I'll change the name of the aggregation

to purchased_ranges and the field should be

purchased_at and I'll also need to change

the aggregation type to date_range.

So the objects within the ranges parameters

should also contain the from and to key,

so no difference there, but the values should be different.

So the 1st of January, 2016.

Then for the to key, I'll be using some date math.

So I'll take the same date as above.

Oops, 2016-01-01, and then I'll add two pipes

using date math and write +6M for six months.

So that's one range.

Then let's get to work on the other one.

From, and for this key, I can use

this date math expression right here like so.

Let's add to key as well.

So 2016-01-01, the 1st of January, two pipes plus one year.

Let's run the query and take a look at the results.

We get both textual representations

and the number of milliseconds since the epoch back.

We'll change the keys to be more readable in a moment,

but first I want to direct your attention

at the from_as_string and to_as_string values.

Notice how the value for the to_as_string key

for the first bucket is the 1st of July, 2016.

You might wonder if that wasn't supposed to be

the 30th of June instead and if we made a mistake

when using date math.

The values are actually correct.

Remember that the from value is included

and the to value is excluded.

This is also the case for the date range aggregation.

So we essentially specified that the date

for the first bucket must be before midnight

on the 1st of July, 2016.

That's why the dates are correct.

If the to value was included,

this would be a different story.

Okay, so the textual representations

of the from and to values are not so pretty.

And that might be fine if we are using

the fields to pass the dates in our application.

But maybe we just want dates back without times

or maybe we just want the dates

to be returned in a different format.

We can specify this by adding a formal parameter

to the date range aggregation.

So let's do that below the field parameter.

I'll add a format parameter, like I said,

and here, we can specify the format of the date.

I'll just get rid of this because I want the year first,

then the month, and then the day.

Let's refresh the query.

And you can now see that both the keys

and the textual representations

of the ranges are more readable.

I recommend using the format only if you need to receive

a different date format for your application

and not for the sake of formatting dates for end users.

In my opinion, that's something that

you should do within your application.

But you are of course free

to choose the approach that you prefer.

Let's now see how we can customize the keys

that are returned for each bucket.

The first way is not related to changing the key names,

but rather how the buckets are structured.

Let's try to add a new parameter named keyed

with a value of true and see what happens.

So keyed and true, and let's run it.

You can now see that the buckets now have a key

instead of being anonymous objects.

The key keys from within the bucket objects

have now been moved to be the keys for the objects instead.

But the key names are the same,

so let's see how we can change that.

We simply do that by adding a key parameter

to each range object.

So let's do that for the first range first.

So key.

I'll name this first_half.

I'll do the same for the second range,

of course, with a key of second_half.

Okay, great.

But just receiving the number of orders

is not that much fun.

Let's add a stats aggregation as a sub aggregation

to retrieve some more information about the buckets.

I'll use the total_amount field for the statistics.

Okay, so let's go ahead and add an aggs key

and name the aggregation bucket_stats

and set the type to stats and the field to total_amount.

And of course, run it.

And now we get statistics

in the context of each of the buckets.

That's not something specific to the range aggregations.

And you've seen that before,

but I just wanted to show it once again

because I think it's a really cool and powerful feature.

Anyways, that's how to use range aggregations.

