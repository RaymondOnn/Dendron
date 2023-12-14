---
id: 4iji2lal9e5yvn6li6dbm1c
title: 84_QueryExecutionContext
desc: ''
updated: 1701194243712
created: 1701109633328
---
In this lecture we are going to talk about query execution contexts. You actually saw

an example of it in the previous lecture, but I just didn’t elaborate on the concept then.

In Elasticsearch there are two contexts in which

queries can be executed. The first one is query context.

This is what you have seen thus far with one exception that I’ll get back to.

Besides determining whether or not a document matches the query clause, the query context

also asks the question: “How well does this document match?” As you know, this involves

relevance scoring based on a number of factors that we haven’t discussed the details of yet.

These relevance scores are floating point numbers that are available within the

_score metadata field for each matching document. The higher the relevance score,

the better the document matches the query. In other words, the more relevant the document is.

The results that we get back from the Search API are sorted by these relevance scores in

descending order, such that the most relevant documents appear first. Nothing new here, really.

So when is a query clause executed in a query context, then?

To answer that question, let’s look at a simple search query.

The answer is actually right in front of us.

Notice the key named query at the root of the request body?

That indicates that the query clauses nested within this key are executed in a query context,

meaning that documents are rated based on relevance.

Let’s now talk about the second execution context, which is the filter context.

Query clauses that are run in a filter context answer the question: “Does this document match?”

The key thing here is that it’s a simple yes or no question. Either a document matches,

or it doesn’t. No relevance scores are calculated, so Elasticsearch doesn’t answer the question of

how well the documents match. The filter context is therefore used to filter data,

which usually involves structured data such as dates, numbers, and the keyword data type.

Basically anything where relevance scoring either doesn’t make sense,

or where we just don’t care about it. The benefit of this execution context is that Elasticsearch

doesn’t spend resources on calculating relevance scores, and the results can be cached.

Does all of this sound familiar? I hope it does, because we talked about this in

the previous lecture, in the context of the bool query. Consider the following query.

The bool query runs in a query context as indicated by the query key.

However, query clauses within both the filter and must_not occurrence types run in a filter context.

This means that they don’t affect relevance scoring and may be cached.

In situations where you don’t need relevance scoring, it’s a

good optimization to move query clauses from the must occurrence type to filter.

It’s therefore possible to change the execution context to the filter

context in situations where we don’t need relevance scoring.

Only a few queries support this, so you will often find yourself doing it with the bool query.

The same can be done within aggregations, but we will get to that later. Queries that do support

the filter context generally have a parameter named filter, so that’s pretty consistent.

So, to wrap up, queries are executed in a query context unless specified otherwise.

Relevance scores are calculated within the query context,

and so queries within this context answer the question “How well does this document match?”

For some queries it’s possible to change the execution context to the filter context. Doing so

means that no relevance scores will be calculated, and so the question is “Does this document match?”

That’s simply a boolean answer, i.e. there is no how well a document matches.

Not calculating relevance scores is by itself a performance improvement.

Additionally, query clauses within the filter context may be cached for even better performance.

When filtering documents, you should ask yourself if you need relevance scoring or if you just want

to filter out documents that don’t match the criteria. If you don’t need relevance scoring,

use the filter context whenever possible. For small indices, the difference probably won’t be

noticeable, but it will be for large indices, so try to do it right from the beginning.

Anyway, those are the two execution contexts in Elasticsearch, typically

used within the bool query and the filter aggregation, which we will cover later.