---
id: je6tr54veo4tilvrvbsxtr5
title: 78_MatchQuery
desc: ''
updated: 1701194098807
created: 1701109491439
---
As for our first full text query, we will be using a query named match. The match query is the most

widely used full text query. It returns documents that contain one or more specified terms.

The inner workings of it is basically what I showed you in the previous lecture,

being that the query is analyzed and the result is then looked up in the field’s inverted index.

Let’s type out a query that searches the name field for the word “pasta.”

As you can see, the query matches 12 products,

meaning that they contain “pasta” within their names. Just to reiterate, “pasta”

can appear anywhere within the values since we are no longer searching for exact matches.

Just to show you that the value we are searching for is indeed analyzed,

let’s change it to uppercase letters.

The results are exactly the same. What happened internally is that “PASTA” was

run through the standard analyzer, which contains the lowercase token filter.

That’s why both versions of the query searched for “pasta” in lowercase letters,

which is also how the term is stored within the inverted index.

Our current query only consists of a single word. We haven’t really talked about what happens if we

search for multiple words, something that’s common within a search field.

Instead of just searching for “pasta,” let’s search for “chicken” as well.

Before running this query, let’s take a look at how this works.

The value is analyzed in the same way as before, only this time the result is two terms instead

of just one. So how does this work in terms of the inverted index? Well, the short and

simplified answer is that both of these terms are looked up to see which documents contain them.

Let’s run the query and see what the results look like in more detail.

Scrolling through the results, we will find products containing both of the terms,

as well as ones containing just one of them.

Let’s take a look at why this is the case,

i.e. why both terms are not required for a document to match.

The match query actually has a parameter named operator, which controls the behavior of the query

in situations where the analyzer yields multiple terms. The parameter is set to "OR" by default,

meaning that a document matches if either or both terms appear within the field’s value.

If we explicitly set the operator parameter to "AND",

both terms are required for a document to match.

Let’s try that out in practice by making a copy of our query and then adding the parameter.

The syntax we have been using thus far has actually been a shorthand syntax.

Now that we need to add a parameter, we need to restructure the query a bit. In particular,

the key containing the field name should have an object as its value instead.

We then add a parameter named query containing the value we want to search for.

And finally the operator parameter.

You can of course use this syntax without any parameters as well if

you prefer. It’s a bit more verbose, so that’s just a matter of preference.

If we run the query, we should only see documents containing both terms within the name field.

And indeed that’s the case.

Those are the basics of the match query. It’s one of the most fundamental queries in Elasticsearch,

so chances are you will use it for most of your full text searches.

Don’t be mistaken, though; it’s a quite powerful query,

especially when using some of its advanced parameters, which we will get to a bit later.

As a last thing, I want to mention that you can also use the match query when searching

for numbers, dates, and boolean values. So if you have a search field, the query is pretty forgiving

with anything you throw at it. If the search is not based on user input, then I do prefer to use

term level queries for these data types instead. But again, that’s just a matter of preference.

I’ll see you in the next lecture!