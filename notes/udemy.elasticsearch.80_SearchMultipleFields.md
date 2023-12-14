---
id: 4nnf1gynaia7kog8ud0cofq
title: 80_SearchMultipleFields
desc: ''
updated: 1701194136422
created: 1701109537076
---
You just saw the match query in action. That was great for searching a single field,

but what if we want to search multiple fields? There are a few ways of doing this,

one of which is to use a query named multi_match. That’s what we will look at in this lecture.

The syntax is pretty straightforward,

so let me just type out a query that searches both the name and tags fields for “vegetable.”

Alright, no big surprises here. Let’s run it and check the results.

Quite a few documents match the query.

If we take a look at the first document, we can see that the word “Vegetable” appears

within the name field. And, for the second document, it appears within the tags field.

Therefore we were indeed successful in searching both fields at the same time.

To be clear, a document matches if it contains the term for at least one of the fields.

Now that was easy, right? Don’t be fooled though, because the multi_match query is

actually a very advanced query, so let’s take a look at what else it has to offer.

A very useful feature is that we can adjust the relevance scoring. The way it works is that we

can boost the relevance score per field to adjust the importance of fields. If a document matches

the query for a boosted field, its relevance score is boosted by a number that we define.

Consider the following example. A lot of products contain the “Vegetable” tag,

so products with the term within their name might stand out as being a more significant match.

If we want to boost documents where this is the case, we can apply a relevance boost to the name field.

The way that’s done is by specifying a caret symbol after the field name, followed by a number.

To multiply the relevance score for such documents by two, the query would look as follows.

This effectively means that relevance scores

are multiplied by two when documents contain the term for the name field.

Running the query, we can see that the relevance score for the first document

has indeed doubled. If you didn’t pay attention to what the score was before,

then I hope you’ll take my word for it. ;-)

Let’s now take a look at how the multi_match query works.

Elasticsearch actually rewrites the query internally to look something like this.

As you can see, two match queries are constructed - one for each field.

If at least one of them matches for a document, then that document will be part of the results.

Technically, this is a slight simplification, but it’s close enough.

In terms of relevance scoring, this is where things get slightly more complicated.

There is a type parameter that can be used to adjust how relevance scores are calculated.

That stuff is pretty advanced, so I will just explain how the query works by default.

If you need to adjust the relevance scoring differently, then I encourage

you to check out the documentation, which I have attached to the lecture.

The default behavior is that the relevance score

of the best matching field is used for the document.

Suppose that we search the name and description fields for two terms; “vegetable” and “broth.”

As you know, this query would be translated into two match queries internally.

Within the first example document, you can see that the name field contains both terms,

while the description field only contains the “vegetable” term.

Without getting into the specifics, the Elasticsearch relevance algorithm gives

the name field a higher score. That’s the case for the two other documents as well.

Note that these are just arbitrary relevance scores that I came up with,

so don’t worry about the specific numbers.

By default, the multi_match query will evaluate the scores for each of a document’s fields and

take the highest one. In this case that will be the ones for the name field.

These relevance scores then become what you see for the _score fields

within the search results, i.e. the documents’ relevance scores.

The idea is that multiple fields are searched, but that the best matching

field for each document is used. Once the relevance scores for all matching documents

have been calculated, the documents are sorted by these values as normal.

I do want to show you one way of adjusting this default behavior;

with something called a tie breaker. By default, multiple fields are searched, but only one field

per document is taken into account in terms of relevance scoring. We might want to “reward”

documents where multiple fields match. By defining a tie breaker, we can incorporate

all matching fields into the relevance scores for the documents. Let’s take a look at an example to

see how this works. In particular, the same query as before, but with the tie_breaker parameter.

Within the example document, I added relevance scores for each of the two fields. As you know,

the default behavior would be to select the highest one - being the name field - and

use that for the document. With the tie breaker, however, the behavior is as follows.

The highest score is still selected, which is the name field. This gives us a score of 12.69.

For every other field that matches, Elasticsearch then takes those relevance scores and multiplies

them with our tie breaker. For the description field, the 8.51 score would be multiplied by 0.3,

which is 2.55. This number is then added to the best matching field’s relevance score.

The result of this is then the relevance score for the document.

Here is what it would look like if we were to search four fields with three of them matching.

Hopefully that all made sense.

The point of doing this is that each document gets a relevance boost for each additional

field that matches besides the best matching field. That makes sense because if some of

the terms appear in fields with a lower relevance score, this still signals some relevance that you

might want to take into account. It might not always make sense depending on your use case.

You might also want to tweak the tie breaker to a different number.

So that’s about it for the multi_match query. Be sure to check the documentation if you are

curious about the more advanced ways that the relevance scoring can be adjusted.

I will see you in the next lecture!