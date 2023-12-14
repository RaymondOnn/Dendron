---
id: 4w2ds61gzq19bk2gojwtan3
title: 75_PrefixesWildcardsRegex
desc: ''
updated: 1701194022294
created: 1701106007893
---
I mentioned that term level queries are used for exact matches. That’s because we

are querying values that were not analyzed with queries that are not analyzed either.

There are a few exceptions to this, three of which we will cover in this lecture. Namely

querying by prefix, wildcards, and regular expressions. These queries provide us with a

bit more flexibility than simply matching exact values. You should still use these

queries together with keyword fields for the same reasons I previously mentioned.

Let’s begin by taking a look at the prefix query.

I have prepared an example query in advance, since the syntax is very simple. It simply searches for

documents where the value of the name.keyword field begins with “Past.” Let’s run the query.

As I scroll through the results, notice how the matching documents

contain the words "Pastry" and "Pasta,"

There should be a product with the word “Paste” in here as well;

it’s just outside of the top ten results.

There is one thing that’s important to note with the prefix query - and the following ones as well

for that matter. The prefix that we are searching for must appear at the beginning of the term.

Even though the values we see here include several words,

each of the values are stored within a single term within Elasticsearch.

That’s because the values were not analyzed when the documents were indexed. This means

that even if the word “Pasta” appears at the end of the term, the document will not match.

The prefix must appear at the beginning of the term to be considered a match.

We have a tags field available to us, which contains tags such as “Pasta,” “Paste,” and

“Pastry” among others. We should probably query that field instead, because then we don’t have to

rely on the prefix appearing at the beginning of the product name. That would yield inconsistent

results, because the word “Pasta” could just as well appear at the end of the product name.

The query now matches about 15 more products than before.

If we wanted to match products where the term “Pasta” appears anywhere within a field’s value,

we should use so-called full-text queries. We will look at those soon.

Next, let’s talk about the wildcard query. It’s probably no surprise that this query enables us

to search with wildcards. There are two wildcards available; a question mark and an asterisk.

A question mark matches any single character, while an asterisk matches any character zero

or more times. An asterisk therefore doesn’t require any characters to be present in its place.

On the diagram, you can see a couple of example patterns and whether or not they match a number of terms.

I encourage you to pause the video for a moment if you want to have a closer look.

One important thing to mention is that you shouldn’t place wildcards at the beginning of

a pattern. While it does work, doing so can have significant performance implications,

since it slows down the query. If you only have a fairly low number of documents,

it might not make a noticeable difference, but it might if you have many documents.

And, the number of documents might suddenly increase significantly,

so be cautious about making any assumptions in that regard. So if you can, avoid beginning

patterns with a wildcard and only do so after considering the performance implications.

That’s pretty much it for the wildcard query. Let’s quickly run

two of the example queries just to show you that I am not filling you with lies.

The first query includes a pattern with a question mark,

so we should see it match terms such as “Pasta” and “Paste.”

Scrolling through the results, that is indeed the case.

The next query includes an asterisk wildcard and matches terms such as “Beets,” “Beer,” and “Beef.”

The results for this query also look as expected.

Let’s move on to the last query now, named regexp. I’m sure you guessed it;

it’s used to perform searches with regular expressions. In case you are not familiar

with regular expressions, they are essentially patterns that are used to match strings. The

wildcard query uses patterns too, but the regexp query enables us to define much more complex ones.

Take the following regular expression, for instance.

It matches any string that begins with “Bee,” followed by at least one occurrence of the

letters “f” or “r.” The parentheses form a group, and the pipe symbol is an OR operator.

The plus sign means “one or more times.”

We could also make the pattern a bit more dynamic by including a

character set, meaning that any letter is allowed following the “Bee” prefix.

While it’s fairly straightforward to use regular expressions,

there are a few things to be aware of.

First, you need to match the entire term for a document to match the query.

Consider the following example, for instance.

This pattern matches terms with the “Bee” prefix and exactly one occurrence of either

the letter “r” or “t.” The term “Beet” matches this pattern, while “Beetroot” does not.

That’s because the pattern only matches the first half of the term and not the entire term.

For the term “Beetroot” to match, we would have to include a character set or a wildcard.

In this example I reused a previous pattern with a character set. Here’s another example.

This time the pattern doesn’t include any wildcards, character sets, or the like.

These are not required within a regular expression.

Even though the word “Beer” does appear within both of the terms, the pattern does not match.

Again, that’s because only part of the terms match the pattern.

If we include a wildcard at the end of the pattern,

the term that begins with the word “Beer” will match.

Just to be clear, a period symbol means any character, while the asterisk means

any number of occurrences. That’s not the same as with the wildcard query,

where this is simply accomplished with an asterisk. I just wanted to be clear about that.

If we added the wildcard at the beginning of the pattern instead, only the first term would match.

And both terms would match with wildcards on both sides.

Let’s try to run a query that uses one of the patterns I just showed you.

This pattern matches tags that begin with “Bee”, followed by the letters “f” or “r.”

This effectively matches the “Beef” and “Beer” tags.

When using regular expressions within Elasticsearch, there is one thing to be aware of.

Elasticsearch uses Apache Lucene’s regular expression engine to parse the patterns. Although

the allowed pattern syntax is much the same as with other engines, there are a few differences.

One of them is that anchor operators are not supported, i.e. the caret and dollar symbols.

These mean the beginning and end of a line, respectively.

Instead, we need to match the entire string as we just talked about. On the diagram you can see a

couple of examples of how some regular expressions can be translated into the Lucene pattern syntax.

Alright, just one thing left before wrapping up this lecture.

The queries that I showed you are case sensitive by default. That’s the default behavior for all

term level queries. However, all three queries support a parameter named case_insensitive.

That’s simply a boolean flag that can be used to perform case insensitive searches instead.

You can see an example for each query on your screen.

That’s all for this lecture - I’ll see you in the next one.