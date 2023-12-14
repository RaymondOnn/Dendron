---
id: kxtjhms8h4v6hc5d3425wba
title: 77_IntroFullTextQueries
desc: ''
updated: 1701194080458
created: 1701109472395
---
Now that we have covered term level queries, it’s time to talk about full text queries,

being another group of queries. Where term level queries are used for exact matches on

structured data, full text queries are used for searching through unstructured text data.

A couple of examples are blog posts, news articles, comments, etc. Think of full text

searches as entering a query into a search bar, such as on Google or within a mail application.

In such a scenario we surely cannot search for the entire content of a website or email,

so we naturally search for specific words or phrases.

That’s what full text searches are all about;

finding the documents that contain certain terms within unstructured text.

Although not a requirement, these text values are often quite long, which is why we cannot

feasibly perform exact matches on them. As you know, that’s a job for term level queries.

Suppose that we have a posts index containing a number of blog posts.

Here is one example of what a document looks like.

The title and body fields are of the text data type. If we were to search for the

word “sharding,” this document would match because it contains the word several times.

That’s essentially what full text queries are about; finding the documents that contain one

or more terms for a given field. Notice how I said contain, meaning that the field value

doesn’t necessarily have to be an exact match. In fact, that is rarely the case

with full text searches, because that’s what term level queries are used for.

You can think of the field value as a haystack and the value we are searching for as a needle.

Now that you know what full text queries are used for, let’s take a look at how they actually work.

That should also make it more clear how they differ from term level queries.

One of the most important things to know about full text queries is that the query is analyzed.

Suppose that we are searching the body field for “SHARDING.” When processing the query,

Elasticsearch will inspect the mapping for the field. If an analyzer is configured,

it will use that one. Otherwise it will default to the standard analyzer. The query will then

be run through this analyzer in exactly the same way as when documents are indexed. The reason for

doing this is that the values that are stored within the inverted index have been analyzed.

To make sure we are not comparing different things, both values are analyzed in the same way.

If this were not the case, searching for the word “sharding” in all uppercase letters

would not match anything, because all terms within the inverted index were lowercased.

Since the term we are searching for is analyzed, it matches the “format” of the inverted index.

A bit later in the course you will see how this analysis process is

key to making things such as synonyms work, for instance.

So, the query is analyzed with the field mapping’s analyzer. The resulting term is

then used for a lookup within the inverted index. If there is a match, the documents containing the

term are the ones that match the query. These are then returned as part of the query results.

The fact that full text queries are analyzed is also the main difference between those and term

level queries. Term level queries are not analyzed and are therefore used for exact matching. Since

full text queries are analyzed, these are not used for exact matching. A bit earlier I explained why

term level queries should not be used for fields of the text data type. For full text queries,

it’s the other way around; they shouldn’t be used with the keyword data type. That’s because

the values for such fields were not analyzed, but full text queries are. Hopefully that makes sense.

Alright, so those were the basics of full text queries. To summarize, full text queries are used

to search through unstructured text values such as blog posts, emails, news articles,  etc.

Unlike term level queries, full text queries are not used for exact matching,

but rather for finding the documents in which one or more terms appear. For instance,

if the term “sharding” appears among many other terms within a blog post, the document will match.

This behavior is made possible by the fact that both the indexed values and the search

queries are analyzed with the same analyzer. This is also the reason why full text queries

should not be used for keyword fields, because the values for such fields were not analyzed.

Now that you understand the basics of full text queries, let’s write one.