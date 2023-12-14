---
id: 5tort86nc58iizy050fpqa1
title: 81_PhraseSearches
desc: ''
updated: 1701194157630
created: 1701109566780
---

A bit earlier we covered the match query. By default, a value matches

if at least one of the specified terms appears anywhere within the field value.

The terms may appear in any order as shown in the following example.

Here you can see how searching for “Fanta Zero” and “Zero Fanta” will yield the same results.

The terms also don’t have to be adjacent as shown in this query that searches the description field.

Let’s now talk about phrase searches, which share many similarities to the match query, hence why

I mentioned it. A phrase is a sequence of one or more words, such as “Browse the Internet.”

In Elasticsearch, we use the match_phrase query to search for phrases.

When doing so, the order of the words matters, which differs from how the match query works.

Let’s look at an example to illustrate the difference.

First of all, the match_phrase query is analyzed

in the same way as the match query - usually with the standard analyzer.

Nothing new here. Let’s go through a handful of example documents and evaluate whether or not

they are matched by the query. Matching terms have been underlined for your convenience.

Also notice that I am showing the actual documents and not the inverted index.

That’s just to keep things a little simpler.

While the first document does indeed contain all of the terms we are searching for,

it’s not matched by the query. That’s despite the fact that they even appear in the correct order.

The reason there is no match is that the terms are not adjacent, i.e. next to each other.

Notice how there are two words in-between the ones we are searching for, which

is why the phrase doesn’t appear anywhere. For a phrase to match,

the terms must appear in the correct order and with no other terms in-between.

The next document is not matched either because one of the terms is missing and the order of

terms is different. For phrase searches it’s a requirement that all terms are present,

which is unlike the default behavior of the match query.

Additionally, there are two other terms in-between the matching ones.

The third document also doesn’t match. All of the terms appear, but they are not in the

correct order. Note that the values that you see here would also be analyzed and stored within an

inverted index. Therefore the parentheses would be removed as part of this process,

so these make no difference; if the terms were in the correct order, the query would

match regardless of the parentheses or any other symbols that might be present.

The next document does indeed match because the phrase appears within the field value.

This means that all of the terms appear next to each other and in the correct order,

matching the phrase that we are searching for.

The same is the case for the last document, where the hyphen is dropped during the analysis process,

exactly as the parentheses are for the third document.

Alright, so how do these phrase searches actually work? How does Elasticsearch

efficiently find the documents where the terms appear in a specific order?

It’s one thing to find the documents that contain the terms,

but Elasticsearch also needs to keep track of where the terms appear within field values.

I don’t want to go into too much detail, because we will look at this in a bit more detail later.

But to give you a high level explanation,

Elasticsearch stores a bit more information within inverted indices than I showed you thus far.

Here are two documents and the corresponding inverted index.

This is how I have illustrated inverted indices thus far. However, this only contains information

about which documents contain a given term, which is not enough information to perform

phrase searches. Therefore Elasticsearch needs to keep track of the positions of each term

within documents. This information is actually recorded during the analysis process by default.

When a string is indexed into a text field, the analyzer will record at which position

each term occurred. Specifically, this task is handled by the analyzer’s tokenizer.

These positions are then stored within the field’s inverted index as follows.

For each document that contains a given term, the position of it is stored.

Note that a term may appear multiple times within the same field value,

in which case all positions are stored. This is just not the case for our two example documents.

When running a phrase query, Elasticsearch uses these positions to check if the terms

appear in the correct order. Consider the following query, for example.

The query is analyzed as you would expect, yielding the following terms.

First, Elasticsearch performs a lookup within the inverted index for the description field

to resolve which documents contain the two terms. That’s both documents in this example.

Next, Elasticsearch needs to figure out if the terms appear as a phrase, i.e. next to each

other in the correct order. That’s basically just a matter of ordering the terms in the

same order as within the query and checking if the positions are in increments of one.

Based on the positions, it’s easy to determine that neither of the documents contain the two

terms as a phrase. For that to be the case, the positions of the term “guide” would have

to be three and five, respectively, i.e. one greater than those of the “elasticsearch” term.

Based on the term positions, we can also visualize the documents like this.

As you can see, there are gaps between the terms for both documents, and they are also in the wrong

order for the second document. That’s just a different way of visualizing the same thing.

Let’s look at an example of a query that does match a document.

First, here are two new example documents along with the corresponding inverted index.

Then suppose we search for the phrase “guide to elasticsearch” as follows.

Based on the analyzed query,

the inverted index tells us that both documents contain all three terms.

Based on the term positions, we can construct the following.

The first document doesn’t match because there are two terms in the way, so to speak. The second

one does, however, as all three terms appear next to each other and in the correct order.

Now that you have seen a couple of queries within diagrams, let’s actually run some. I won’t cover

anything new from now, so feel free to skip to the next lecture if you have seen enough.

Anyway, I have four simple phrase queries prepared. Let’s quickly run them one by one.

The first one, which searches for “mango juice” doesn’t match anything. Let’s try the second one.

This one searches for the phrase “juice mango,” which matches one document. The terms are the

same, but they are now in the same order as within the document, hence why we get a match.

Notice how the hyphen doesn’t affect the matching, because it was dropped during the

analysis process. That’s also why adding symbols within queries won’t have any effect either.

As you can see, the document still matches despite the parentheses.

Last but not least, let’s search the description field for a phrase consisting of three words.

This query matches one document as well.

As we can see, the phrase does appear in the middle of the value for the description field.

Those weren’t the most interesting queries of all time, I know,

but that was just to quickly show you phrase queries in action.

That’s all for this lecture; you should now have a

solid understanding of how to use the match_phrase query and how it works.