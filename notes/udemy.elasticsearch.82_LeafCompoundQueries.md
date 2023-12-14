---
id: xjokqizjt400l9qui34sc3k
title: 82_LeafCompoundQueries
desc: ''
updated: 1701194185181
created: 1701109587261
---
We have just covered both term level queries and full text queries.

I haven’t told you this, but all of these queries are actually referred to as leaf queries.

In the following lectures we will look at a few so-called compound queries. So what’s the difference?

A leaf query is a query that searches for a value within one or more fields.

These queries can be used by themselves. An example could be the term query.

Every term level query and full text query that we covered thus far fall into this category.

A compound query is one that wraps other queries to combine them logically.

In other words, they orchestrate other queries to produce a result.

A simple example could be to search for products that contain the “Alcohol” tag and that are almost

sold out. This would involve two queries; a term query and a range query - both leaf queries.

In Elasticsearch, we can only define a single query at the top level of our request,

meaning that these two leaf queries need to be wrapped within a compound query.

Thus far, we covered a number of different search queries, but we only ran one at a time.

If you want to combine them, you need to wrap them within a compound query.

For advanced queries, it’s actually possible to nest this even further,

such as nesting a compound query within another compound query.

Compound queries therefore wrap leaf queries or other compound queries.

This might sound a bit confusing, so here is a quick example.

Suppose that we want to find products with the “Alcohol” tag. In addition to that,

it should either be sold out or be inactive, i.e. no longer for sale.

In SQL, that could look something like this.

In Elasticsearch, this would give us three leaf queries.

To construct the boolean logic, we would wrap them within compound queries, i.e. something like this.

As you can see, compound queries may contain both leaf queries and other compound queries as well.

This enables us to write very powerful and complex queries.

Let’s now look at some compound queries. I’ll see you in the next lecture!