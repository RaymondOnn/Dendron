---
id: z21tk01z7nukmo81u1qsy2v
title: 79_IntroRelevanceScoring
desc: ''
updated: 1701194117612
created: 1701109517087
---
In the previous lecture I ran a query that searched for both

“pasta” and “chicken.” You can see the query and its results on your screen.

As you know, a document will match if either or both of the terms appear within its name field.

Did you notice, however, that the documents at the top of the results contain both of the terms?

That’s because there is an important concept that we haven’t talked about yet; relevance scoring.

Apart from the match query, we only covered term level queries thus far. You might have noticed

that the documents that matched these queries all had a value of 1.0 for the _score field.

I just didn’t mention it, because relevance scoring is generally not relevant for term level queries.

That’s because we are basically evaluating a condition that is either true or false;

either a document matches, or it doesn’t. There are exceptions, but this is generally the case.

With full text queries, however, we are no longer doing exact matching,

so it’s not just about whether or not a document matches. We also care about how well it matches.

Consider the Google results for a search query, for instance.

We surely expect the best results to be at the top and not on page 201.

The same applies to full text queries in Elasticsearch. Specifically, search results

are sorted so that the matches with the highest value for the _score field appear at the top.

In other words, the results are sorted descendingly by the _score meta field.

This explains why documents containing both terms from the search query appeared

first within the results; these were scored higher because they matched

the query better than the documents that contained only one of the terms.

Don’t worry about the specific numbers that you see for the _score field.

There are a lot of factors that are considered when calculating relevance scores, so it’s

actually pretty complicated. It’s totally normal if you don’t see the exact same numbers as me.

We won’t dive into how Elasticsearch calculates the scores now, because

we will revisit the topic in more detail a bit later in the course.

For now, I just want you to understand how relevance scoring works at a high level.

As you know, full text queries are analyzed, and then the resulting terms are looked up

within the inverted index for the field. Nothing new here. Once Elasticsearch has

looked up which documents match the query, it calculates relevance scores for each of them.

When that’s done, the documents are sorted based on the relevance scores to form the query results.

Of course that’s not everything that happens, but that’s a high level overview.

That’s really all I want you to know about relevance scoring for now, so let’s continue.