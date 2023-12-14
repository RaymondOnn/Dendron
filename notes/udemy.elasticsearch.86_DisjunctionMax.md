---
id: sedvjmx9n2gyk9f8leq7qfv
title: 86_DisjunctionMax
desc: ''
updated: 1701194277779
created: 1701109675063
---

In this lecture we are going to talk about a query named disjunction max, also referred

to as dis_max. Let’s just get right into it with an example and see what it’s all about.

As you can see, it’s another compound query which contains query clauses,

being either leaf queries or compound queries themselves. A document matches the dis_max

query if at least one of the specified query clauses match. In this example,

a document matches if it contains the term “vegetable” within either its name or tags field.

By default, the best matching query clause is used to calculate the relevance score of a

matching document. In other words, the highest relevance score is assigned to the document.

As you can see, the match query clause that searches the tags field yielded

the highest relevance score, and so that score was used for the matching document.

Note that I just made up both the document and the relevance scores.

If only one query clause matches, that relevance score will naturally

be used. That’s probably no surprise, but anyway…

This is how the dis_max query works by default. We can also supply a so-called tie breaker,

which is a way to “reward” documents that match multiple query clauses.

You might recall us talking about this before in the context of the multi_match query.

The behavior is the same for the dis_max query, but here is a quick recap anyway.

Suppose that a document matches both query clauses, which was the case for our first example.

As you know, the default behavior is to just grab the highest relevance score and use that for the

matching document. Now consider the following query which adds the tie_breaker parameter.

Its value should be a floating point number between 0.0 and 1.0.

It defaults to 0.0, which gives the default behavior you just saw.

Let’s take the same document as before and see what that does to the relevance scores.

First, Elasticsearch finds the highest relevance score among the matching query

clauses. That’s 5.62 in this example. If there are any other matching query clauses,

those relevance scores are multiplied by the tie breaker, being 0.3. The resulting number is then

added to the highest relevance score. This process repeats for any other matching query clauses.

The resulting number is then used as the relevance score for the document.

This approach means that the relevance scores of documents are increased for

every query clause they match. This is similar to the bool query’s should occurrence type,

although the calculation is different.

With the dis_max query, we can control how much the relevance scores should be boosted.

So, did all of this sound familiar? Hopefully it did, because this all relates to the

multi_match query that we previously talked about. Remember this diagram?

It shows how a multi_match query with default parameter values is translated into two match

queries. As I mentioned at the time, that was a bit of a simplification. While it’s true that a

match query is constructed for each field, these leaf queries need to be wrapped within a compound

query. That’s simply because we can only run one “root query” at a time, so to speak.

So what actually happens is that these match queries are wrapped within a dis_max query as follows.

As you can see, that’s a very basic dis_max query. If we specify a tie breaker for the

multi_match query, that parameter is also added to the dis_max query.

Note that exactly how the multi_match query is translated depends on which parameters you add

to it. This example shows how it’s translated when the type parameter is set to best_fields,

which is the default value. Regardless, the multi_match query doesn’t do any magic for us;

rather, it’s just a convenience query that makes it easier for us to write queries.

A bit of an abstraction, you could say.

I didn’t mention all of this at the time because at that point we had covered neither

compound queries nor the dis_max query. Now that you know what both of those are,

I did want to show you how the multi_match query works internally, at least for its simplest usage.

That’s it for the dis_max query. It’s a useful query

when you have multiple query clauses that don’t necessarily need to match.

You can then either let Elasticsearch take the highest relevance score for each document,

or optionally give a relevance boost for each additional query clause that matches.

I’ll see you in the next lecture!