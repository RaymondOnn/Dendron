---
id: ogxo75eor6cd8e78pgl6k39
title: 70_IntroSearching
desc: ''
updated: 1701193888621
created: 1701105875874
---
Now that we have looked at mapping and analyzers, it’s finally time to get into

searching for data. It did take us a while to get here, but that’s because both mapping

and analysis is essential to understand before getting started with search queries.

Alright, so there are two ways in which we can write search

queries. The first one is by including search queries as a query parameter,

which is referred to as a URI search. You can see an example of that on your screen.

The value of the query parameter should be in Apache Lucene’s query string syntax.

I am not going to cover URI searches in this course, because they are somewhat rarely used.

The reason is that it’s a simplified search that doesn’t offer all of the features that

Elasticsearch provides. Plus, you would have to get familiar with Lucene’s query string syntax.

Instead, I am going to cover something called the Query DSL, which is the preferred way of writing

search queries. Instead of embedding the query within a query parameter,

it’s added within the request body, meaning that it should be in JSON.

The two approaches use the same request path — and thereby API — being the Search API.

As you can see within the example, the Query DSL can sometimes look a bit more complicated, because

it’s more verbose. That’s just a compromise for getting access to all of the search features.

It does look worse than it really is; you will quickly get used to the syntax,

so don’t be intimidated by it. And fortunately,

Kibana’s Console tool also helps us with autocompletion when writing out the queries.

Now that you know which approaches are at our disposal, let’s write our first

search query. Both the HTTP verb and request path will be the same

as in the examples that you just saw, so let me just type that out.

Next, we need to add a request body, being a JSON object.

Here we can add a number of parameters to configure the search request,

such as for pagination or sorting. The search query itself is defined

within an object named “query,” so let’s go ahead and add that.

Within this object, we define the type of query as a key. Let’s go with the simplest possible search

query, being the match_all query. It’s probably no surprise that this query matches all documents.

The value for this and any other search query must be an object, being where we

can add any parameters to the query. That’s not applicable to this super simple query, though.

That’s it for the most basic search query, so let’s go ahead and run it.

Now that the query has been run,

let’s take a short moment to inspect the results that appeared to the right.

The took key is simply the time it took for Elasticsearch to execute the request

from the time it was received. Specifically, the number of milliseconds the request took.

The value for the timed_out key is simply a

boolean flag for whether or not the request timed out.

Moving on to the _shards key, we can see that its value is an object containing four keys,

all of which contain integer values. The total key specifies how many shards should be queried

to execute the request. Note that this number includes shards that haven’t been allocated,

so if the number is higher than you expect, then that is probably the reason.

The successful and failed keys specify the number of shards that executed the request

successfully or unsuccessfully, respectively. Lastly, the skipped key represents the number

of shards that skipped the request. A shard may skip executing the request if it determines that

it contains no documents that can possibly match the request. This typically occurs for range

values where the shard only stores documents that fall outside of the specified range.

The hits key contains the documents that matched the query along with some metadata.

Within this object, we have a total key that shows us how many documents matched the query.

The nested relation key contains a value of “eq” if the value key contains an accurate number,

which will almost always be the case. There is a way to do some performance

tuning of queries at the tradeoff of the value not being accurate. In that case the

key will contain a value of “gte,” which is short for “greater than or equal to.”

That’s not a topic we should get into right now, though, so let’s move on.

The max_score key contains the highest document score that was calculated by Elasticsearch.

We’ll get into relevance scoring soon, but essentially Elasticsearch determines how

well a given document matches the query and calculates a score based on that.

This field therefore contains the highest relevance score of any matching document.

In this case it’s simply 1.0 because we used the match_all query. That query always just

matches all documents, so there was no need for Elasticsearch to compute any relevance scores.

Then we actually have a nested key named hits. And yes, in case you are wondering, this hits key

is indeed nested within a hits key. Could Elastic have chosen better names for these keys? Probably,

but anyway… This key contains the actual documents that matched the query along with a bit

of metadata for each document. First, we have the name of the index in which the document is stored.

You might be wondering if that’s not redundant since we specified the index name within the query.

Typically you would be right, but when searching for data we can actually search

multiple indices at once, so that’s why we might not always know the index name ahead of time.

Then we have the unique identifier of the document, followed by its relevance score.

The _ignored key contains the names of fields that were ignored when the

document was indexed. For this document that was the description.keyword field.

The reason is that the mapping contains the ignore_above parameter with a value of 256,

meaning that values longer than this are ignored. That’s how text fields are mapped by default with

dynamic mapping. Lastly, the actual document is returned within the _source key. As the key

name suggests, this is the _source object that we discussed earlier in the course.

Those were the absolute basics of how to search for data. Simply matching all documents

is not that useful, so we will cover many different query types throughout this section.

These can be used to define criteria that documents should match.

Let’s continue with some more useful queries.