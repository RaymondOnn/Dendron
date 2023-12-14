---
id: mznxeclffr9wd5jhmp0mrso
title: 106_SpecifyOffsets
desc: ''
updated: 1701194811018
created: 1701192874500
---
-: You just saw how to specify the maximum number of hits

returned by a query.

But what about retrieving the next page of results?

If the size parameter is set to 10,

it's likely that we want to retrieve the next 10 results

at some point.

To do this, we can use an offset

indicating the number of matches to skip

before returning the matches.

The combination of the size parameter and an offset

then creates a sliding window

enabling you to implement pagination.

We do this by specifying a from parameter

which defaults to zero.

As with the size parameter,

this parameter can be specified either as a query parameter

or as a parameter within the request body.

I'll be doing the latter in this lecture.

So, let's begin by adding a from parameter to the query

from the previous lecture with an initial value of zero.

That's the default value so this is really redundant

but we'll of course be changing the value.

So, let's add this parameter named "from"

and specify zero as the value.

And let's just run the query for good measure.

So, this query is no different

than if we had not specified a from parameter

because it just retrieves the first two hits.

To retrieve the next two hits,

we can simply change the value of the from parameter to two.

So, let's do that and run the query again.

So, we just retrieved the first two pages of search results.

To retrieve the third page,

we will just change the from parameter to four.

If you have experience with relational databases,

then you can think of the size parameter

as the equivalent of the limit clause in SQL.

The from parameter is equivalent

to the offset clause in SQL.

And the combination of the two is equivalent

to MySQL's limit clause that contains

both a row limit and an offset.

You've already seen how to use the size and from parameters

to retrieve a specific number of documents

at different offsets

which is the same approach that we need to use

for implementing pagination.

We haven't talked about these two parameters

directly in the context of pagination

so let's do that in the next lecture.

