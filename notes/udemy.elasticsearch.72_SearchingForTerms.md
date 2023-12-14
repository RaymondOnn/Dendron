---
id: xc9qu1zooonawj1hlppdbxn
title: 72_SearchingForTerms
desc: ''
updated: 1701193930209
created: 1701105921066
---
Now that you know the basics of term level queries,

let’s have a closer look at them. One of the most important ones is the term query,

which you already saw a preview of in the previous lecture. Let’s see it in action.

I have prepared the basic structure of a search query,

since you have seen that before as well. Let’s add a term query to it.

Within this object we simply add a key containing the name of the field that

we would like to search. The key’s value is the value that we want to search for.

Let’s search for products containing the “Vegetable” tag.

Notice how I used the keyword mapping instead of

the text mapping. The reason is what I explained in the previous lecture,

being that term level queries are not analyzed and are therefore used for exact matching.

Also notice that even though the test data specified arrays for the tags field,

we don’t need to handle this within search queries. Because every field in Elasticsearch may

contain zero or more values, we simply enter the name of the field and everything will be handled

for us automatically. In this example, a document will match if it contains the “vegetable” tag.

Anyway, let’s run the query.

As you can see, it doesn’t match anything. Come to think about it,

all of the product tags do begin with a capital letter, so let’s correct that and try again.

And there we go. That was just to show you that

what I said about term level queries being case sensitive is indeed true.

Besides searching for text values, we can also search for a number of other data types.

To save a bit of time, I will just show a couple of examples on diagrams.

You can find the queries within the GitHub repository in case you want to run them.

The first example is searching for a boolean value, i.e. true or false.

This query simply matches all products that are marked as active.

We can also search for numbers, whether they are integers, doubles, or floating points. In this

case we are matching products that are almost sold out, perhaps because we need to refill our stock.

The term query can also be used for dates, with or without the time part.

This query is useful for matching a specific date. In a couple of lectures,

I will show you a more flexible query which also supports date ranges.

The term queries you saw thus far were all written using a shorthand syntax.

There is a more explicit and verbose syntax, which you might prefer for improved readability.

This query is equivalent to the one you saw earlier.

This more explicit syntax is actually required if we want to specify parameters for our query.

That’s typically not needed for the term query, but I do want to show you an example.

There is actually a parameter that enables us to perform case insensitive searches. Specifically,

the case_insensitive parameter which takes a boolean value.

I will just make a copy of our existing query and show you what it looks like.

Alright, there we go.

To test that it works, let’s search for the tag in lowercase letters and run the query.

Unlike the first time we searched for an all lowercase tag,

the query now matches some documents.

I did say that term level queries require an exact match, which is true for the most part.

This parameter hasn’t always been around, so for many years this was not even possible.

So far, we only searched for a single term. If we want to search for multiple terms,

there is a variation of the term query named terms for exactly that purpose.

We basically use it the same way, except that we provide an array of values instead of just one.

A document will match if it contains at least one of the supplied values.

Let’s adjust the first query to search for multiple terms instead.

This query matches products tagged as either “Soup” or “Meat” - or both.

Logically, the query could be written something like this.

Anyway, let’s run it.

Scrolling through the results, we should see documents containing either of the tags,

and both if we are lucky. Only 10 documents are returned by default, so we’ll see.

So the first one contains the “Meat” tag.

And here is one containing the “Soup” tag.

We should have one containing both tags, but it’s not within the first ten results.

Anyway, that’s all there is to searching for exact terms in Elasticsearch.

Remember to use these queries together with keyword mappings and not full-text fields.