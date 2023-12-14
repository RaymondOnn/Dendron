---
id: rrdx1n0gdo7njldv4145reh
title: 71_IntroTermLevelQueries
desc: ''
updated: 1701193909139
created: 1701105898749
---
In Elasticsearch there are a few groups of queries, one of which is called term level queries.

This group of queries is used to search structured data for exact values,

i.e. filtering documents. An example could be to filter products based on color, brand, price, etc.

There is one important thing that characterizes all term level queries,

being that they are not analyzed. What this means is that the value that we are

searching for is left intact and is used as is to lookup values, such as within an inverted index.

Let’s see what this means by going through an example. Suppose that we have a products index

containing clothing items and that each document contains a brand.keyword field. It’s probably no

surprise that this field contains the name of the brand and is mapped as a keyword field. You can

see an example document on your screen along with the inverted index for the brand.keyword mapping.

Now we want to find all of the products where the brand equals “Nike.” We can do that with a term

level query, since we need to search for an exact value. Here is what such a query could look like.

We are searching the brand.keyword field for the value “Nike” with the term query.

We will cover that particular query in the next lecture. It’s just one of the term level queries,

which there are about a dozen of. Anyway, the query will match our example document since the

value we are searching for equals what is stored within the inverted index. Term level queries

match the exact terms that are stored for a field, meaning that the searches are case sensitive.

So if we were to lowercase the value that we searched for, the document would no longer match.

Matching the exact terms also means that the entire value must match and not just a part of it.

Consider the following example, for instance.

Even though the term we are searching for is part of the value that’s stored within

the inverted index, the search query won’t match the document.

That’s simply because the query only matches some of the term and not the whole term.

Term level queries can be used together with a couple of data types such as keyword, numbers,

dates, etc. Apart from not realizing that term level queries match exact terms,

there is one other mistake that a lot of people make; using term level queries

together with the text data type. Let me illustrate why you should never do this.

Suppose that we want to find all products that contain the term “Nike” within their names.

That’s not an exact match, so we shouldn’t use a term level query for this,

but here is what would happen if we did anyway.

Since the name field is of the text data type, all values for the field were analyzed,

causing the inverted index to look as follows for our example document.

Notice how the product name was tokenized and all letters were lowercased.

Searching for the term “Nike” will not yield any results, because this exact term is not present

within the inverted index. We are comparing an analyzed value with something that isn’t analyzed.

Basically we are trying to compare apples to oranges, and that’s never a good idea.

To be clear, the query doesn’t match because the term within the inverted index is in all

lowercase letters. If we searched for Nike in all lowercase letters instead, we would get a match.

That’s fine, but what if we search for the exact product name?

The query isn’t going to match anything, because the field value was analyzed when

the document was indexed. This caused it to be tokenized among other things, hence why there

is no term matching what we are searching for. That’s because the query itself is not analyzed,

so we are trying to compare something that has been analyzed with something that hasn’t.

So what’s the big deal, you might wonder? The thing about using term level queries on

the text data type is that the results can seem strange and unpredictable at times.

They really aren’t if you know how things work, though, but a lot of people don’t—or they forget.

It just happens a lot that people write a query based on some test data and deploy

it when the results seemingly look good. Later, things won’t always work as expected depending

on the search query, because analyzed data is compared with data that isn’t analyzed.

There is no error telling you that something is wrong. You just don’t get the results that you expect.

If a user searches for “nike”, everything might look just fine,

whereas that would not be the case if they search for “Nike” with an uppercase first letter.

That can be hard to debug if you aren’t aware of how the query actually works.

Thousands of people have been caught off guard by this, so this is very important to understand.

And if you do, you can save yourself from major headaches. Anyway, the takeaway is that you should

never use term level queries on fields with the text data type. As long as you follow that rule,

you should be all good. You can use term level queries for searching text values,

but the fields should have the keyword data types because such fields are not analyzed.

Anyway, those are the basics of term level queries.

To quickly recap, this group of queries should be used for matching exact terms.

That’s because these queries are not analyzed and are therefore case sensitive and must match the

entire value that is being compared. Term level queries should never be used in combination with

fields of the text data type, because these are analyzed while term level queries are not.

Let’s write a couple of queries now.