---
id: hbbsfjtnszpg4voj6bmjlvw
title: 64_AnalyzersAndSearchQueries
desc: ''
updated: 1701193754787
created: 1701105343777
---
I told you how analyzers are used when indexing “text” fields.

That’s only half the story though, as they are also used for search queries.

I might be getting ahead of myself, because we haven’t gotten to search queries yet.

However, you might be wondering how we can search for the values that we index if they

are changed during the analysis process.

Perhaps I got you confused in the previous lecture when we discussed stemming and stop

words, so let me clear up any doubts that you may have.

Let’s take the example from the previous lecture and run it through a custom analyzer.

The analyzer behaves the same as the “standard” analyzer, except that it also stems words.

On the diagram, you can see how the sentence was analyzed.

In particular, the terms were lowercased, some words were stemmed, and the period symbol

was removed as well.

That’s great, but what is going to happen if we search for the word “drinking?”

When indexing the document, the word “drinking” was stemmed to its root form, so will the

document match the query?

Yes, it will.

That’s because the search query is analyzed in the same way as when indexing the field value.

Let me illustrate that on a diagram.

So when the document is indexed, Elasticsearch inspects the mapping for the “description” field.

It detects that it’s a “text” field, which means that it should be analyzed.

It then checks if an analyzer is configured for the field.

If so, that analyzer is used, and otherwise the “standard” analyzer is used.

In this example a custom analyzer named “stemming_analyzer”

is configured for the field, so that one is used in favor of the “standard” analyzer.

It pretty much behaves as the “standard” analyzer with the exception that words for

the English language are stemmed.

The result of running the text through this analyzer is the same as what you saw a moment ago.

Those were the terms that were indexed and stored within the inverted index.

Remember that the original field value is still available within the “_source” object,

but that is not what search queries are run against.

Elasticsearch now receives our search query.

It looks at which fields we are searching, which is the “description” field in this example.

It then does the same thing as when the field value was indexed; it runs the value through

the analyzer that is configured in the field mapping.

That is, it analyzes the value that we are searching for.

The word “drinking” is therefore stemmed by the analyzer, resulting in the term “drink.”

This matches what is stored within the field’s inverted index, and so the document matches

the query.

As you can see, the same analyzer is used both when indexing documents and at search time.

If this were not the case, you would get unpredictable search results.

This is also why queries run against “text” fields are case insensitive by default.

If your query takes the input from a website, it might happen that someone enters a query

in all capitalized letters.

That’s not an issue because the string will be lowercased at search time by the “standard” analyzer.

If a custom analyzer is used, this only happens when it includes the “lowercase” token filter.

The point is that the query is analyzed in the same way as the field values that were indexed.

It’s possible to change this behavior and specify a different analyzer, but that is

rarely needed, so I won’t cover that in this course.

If you do need to do so, you should be very careful, because if you don’t do it right,

you might be in for a world of trouble.

Anyway, hopefully that clears up some potential confusion as to how values can be matched

even though they differ from the ones specified within search queries.