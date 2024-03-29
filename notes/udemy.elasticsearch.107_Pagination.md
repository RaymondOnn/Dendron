---
id: qsyfsfbx0dtz74tfzqmlto0
title: 107_Pagination
desc: ''
updated: 1701194832537
created: 1701192888163
---
Instructor: So we covered the two parameters that we need

in order to implement pagination of search results.

We covered them without really putting them

in the context of pagination.

So let's do that now.

This lecture does not provide any new information

in regards to Elasticsearch,

but just shows how the two parameters that we just discussed

can be used to implement pagination in an application.

Suppose that we're implementing a pagination control

on a website or in any other kind of application.

To do that, we need to know the number

of search result pages that are available.

This is something that you need to calculate

at the application level

based on the size parameter that you specify

and the total number of hits that Elasticsearch returns.

The calculation is as simple as dividing the number of hits

by the page size, i.e. the size parameter.

It's important to round up to the nearest integer

in case there's a remainder for the division.

I've done this in the calculation

by specifying a ceil function,

which you might have seen

from various programming languages.

An example could be that we have 137 hits

with a page size of 10.

This division yields 13.7, which we round up to 14,

which is then the number of total search result pages.

To actually go from one page to another,

we need to specify the from parameter

apart from the size parameter.

We need to calculate that offset.

You could just pass the offset around in the application,

but usually you would want to use a page number

and then calculate the offset based on that.

To calculate the offset, you subtract the page number by one

and multiply that with the page size.

So if we have a page size of 10 and a page number of six,

the offset would be 50.

This is all something that's calculated

at the application level

because Elasticsearch just needs

the size and from parameters to do its job.

Let's go through a quick example with a page size of two.

We have the documents 1 to 10 at the top

and the pagination control with page numbers at the bottom.

The from parameter is zero initially

and then increases by two for every page

according to the formula that you just saw.

So it increases by the page size for each page.

As you can see, this is essentially a sliding window

that moves through the search results

based on the page number

and thereby the size and from parameters.

Later in the course, you'll see how to do this

in an example application.

You should know that this approach is limited

to 10,000 results.

The reason for this is that when performing deep pagination

requests take up more and more heap memory

and requests take longer.

That's why there is a limit of 10,000 acting as a safeguard

so you don't end up affecting the stability of your cluster.

Typically, this is not an issue

because users are highly unlikely

to navigate deeper than the first couple of pages,

but it may be an issue

if you need search engines to crawl deeper.

If you need something like that,

then you can use the parameter named search_after.

This is a bit more complicated

than what I want to get into now,

so I've attached a link to the documentation

to this lecture.

Apart from this limitation,

I also want to briefly mention

how this pagination technique works,

well, or perhaps how it doesn't work.

If you have worked with relational databases before,

then you might be used to working with so-called cursors.

The basic idea is that you have an open cursor

within the database

which you can then use to scroll through search results.

This is not the way it works

when using the size and from parameters in Elasticsearch.

Elasticsearch handles the search query,

and when it returns the results,

it's completely done with it.

At this point, there's nothing left open within the cluster,

no cursors or anything like that.

Each query is therefore stateless.

Apart from some differences in regards to performance,

there is one difference that's worth noting,

specifically, if documents are added, deleted, or modified.

Suppose that a user searches for something

and gets 100 hits.

The user stays on the first page for a minute

and then navigates to the second page.

In the meantime, a new document was added

that relates well to the query,

placing it on the first page.

The user will now see a search result on the second page

which he or she already saw on the first page.

This is just an example.

There could be other scenarios

when modifying or deleting documents.

The point is that since each search query is stateless,

the results are always based on the latest data

and not the data that was available

when running the first query.

This is different from a cursor,

which keeps scrolling through the results

from the point in time where the cursor was opened,

meaning that any data changes do not affect a cursor.

Like I said, there's also some differences

in terms of performance,

but that's another story.

You might not experience this

depending on how many queries

your Elasticsearch cluster handles

and how often documents are added, removed, or modified.

Either way, I just wanted to mention it

so that you're aware of it.

