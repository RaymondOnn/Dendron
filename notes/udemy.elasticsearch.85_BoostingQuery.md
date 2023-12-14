---
id: igmqadgn117nb7mlcukwsf3
title: 85_BoostingQuery
desc: ''
updated: 1701194260862
created: 1701109649652
---
Let’s look at a query that’s kind of similar to the bool query.

With the bool query,  we can boost the relevance scores of documents with the should occurrence type.

But what if we want to do the opposite, i.e. decrease the relevance scores?

We can do just that with the boosting query. Consider the following query.

It simply searches for products that contain the term “juice” within their names.

In case you were wondering, the size parameter just increases the number of matches that are

returned from the default of ten. Okay, so we successfully matched juice products, but come

to think about it, I really don’t like apple juice. Well, that’s a lie, but let’s pretend…

If we look at the results, we will find a product named “Apple Juice” at the top.

There are two more products containing the term “apple” further down.

Introducing the boosting query. It consists of two parts; a “positive” and a “negative”

query clause that we have yet to add. The one defined within the positive parameter

is required to match, exactly as with the bool query’s must occurrence type.

Let’s add the same query clause that you just saw, being the one that matches all juice products.

Within the negative parameter, we specify a query clause that will reduce the relevance

scores of matching documents. Because we are imagining that I don’t like apple juice,

we should write a query clause that matches just that. Since we are already matching juice

products within the positive parameter, we can get away with just searching for the term “apple.”

That’s because the negative query is run in the context of the results from the positive query.

I will just add a match query that searches for “apple.”

So, the boosting query matches any documents that match the positive query clause.

The documents that also match the negative query clause have their relevance scores reduced.

This is pretty much the opposite of the should occurrence type for the bool query.

So how does this actually work? To answer that question, we still need to add a required

parameter named negative_boost. This parameter defines a floating point number between 0 and 1.0.

This number is used as a modifier for documents that match the negative query clause.

In terms of relevance scores, there are two scenarios for documents that match

the boosting query. The first one is that a document only matches the

positive query clause. In this case its relevance score is left intact,

meaning that the score will be the one that was calculated from the query clause.

So the relevance scores you see here are the unmodified scores

that were calculated by the match query defined within the positive parameter.

For documents that also match the negative query clause, their relevance scores will

be multiplied by the modifier, being the value of the negative_boost parameter.

So, the relevance scores that were calculated by the query clause within

the positive parameter is multiplied by 0.5, being the negative_boost parameter.

Let’s head back to Kibana to run our query.

We no longer see the “Apple Juice” product at the top.

If we scroll to the bottom of the results, we can find all of the apple juice products.

These matched the negative query clause and had their relevance scores halved as a result.

The relevance scores of the other documents were left intact.

As you can see, the boosting query enables us to decrease the relevance of some documents,

which we cannot do with the bool query.

In this example we searched for juice products and decreased the relevance of apple juice.

But we might not always want to narrow down or filter the documents in the first place.

What if I just don’t like apples in general, and not just in the context of juice?

Since a positive query clause is required, we need to add something.

So what do we do? We simply add a match_all query.

That may or may not be obvious, but I just wanted to point out

that you don’t have to narrow down the documents within the positive parameter.

The examples you have seen thus far have been pretty simple,

and coming up with simple examples is pretty easy, right? But the real word is often a bit

more complicated, so let me show you a couple of more advanced examples, just for your reference.

These are just made up examples and are not based on our test data. Alright, let’s go!

Here are two simple queries that search an imaginary recipe dataset;

one that boosts recipes with pasta, and one that reduces the relevance of recipes with bacon.

Both include all products, so you can think of these as preferences and not requirements.

Simple stuff. But what if we want to apply the two preferences at the same time?

If pasta were a requirement, it would be easy; we could simply add a term

clause to the boosting query’s positive parameter as follows.

But since we just prefer pasta, we need to make it optional.

The boosting query’s positive and negative parameters only allow us to specify a single

query clause each, so adding multiple ones within an array is not an option.

So what we need to do is to wrap the queries within a compound query. The bool query to the

rescue! While we can only add a single query clause, there is nothing stopping us from

adding a compound query that itself contains multiple query clauses, i.e. leaf queries.

Cool, so we just combined the boosting query with the bool query!

Pasta is no longer required, but recipes that do contain it receive a higher relevance score.

The boosting query then takes care of reducing the relevance score of recipes with bacon,

regardless of whether or not they also contain pasta. And just like that,

we have constructed a query with both “positive” and “negative” preferences, so to speak.

As you can see, the boosting query is quite powerful, especially when nesting

compound queries. You can basically make it as advanced as you want to.

Anyway, that’s all for the boosting query. I’ll see you in the next lecture.