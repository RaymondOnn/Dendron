---
id: 1pmz53nd171o4szfn6cr8fg
title: 68_UpdatingAnalyzers
desc: ''
updated: 1701193839878
created: 1701105447094
---
Sometimes you might have an existing analyzer that you want to update.

As with mapping, you should try to get it right the first time to save yourself from

some additional work, but things change sometimes.

In this lecture, I am going to show you how to update an existing analyzer.

For that purpose I have prepared a number of queries in advance, because you have seen

them all before.

We have an index with a custom analyzer, but we haven’t used it within a field mapping

yet, so let’s do that first.

Particularly, let’s add a “description” field that uses the analyzer named “my_custom_analyzer.”

An index is not much fun without any documents in it, so let’s index a document.

Next, I have a fairly simple search query that searches the “description” field

for the term “that.”

There is one unusual thing about this query, though; it specifies an analyzer.

By default, the analyzer from the “description” field’s mapping would be used to analyze

the query, being the analyzer named “my_custom_analyzer.”

Analyzing the query with this particular analyzer, would remove the word “that” from the

query, causing us to essentially search for nothing.

That’s because the analyzer is configured to remove stop words.

This default behavior is overridden when specifying the “analyzer” parameter.

The “keyword” analyzer is then used instead, which essentially means that the search query

will be left intact.

Our example document was indexed using the custom analyzer, and so stop words were removed.

The term “that” therefore doesn’t appear within the inverted index for the “description”field.

You can see the inverted index to the right.

So what’s the point of this?

Specifying the “keyword” analyzer is a trick to avoid removing stop words from the

query, because we actually want to know if the document contains the term “that.”

You will see why in a moment.

Sorry if this got a bit confusing, but hopefully it will all make sense in a moment.

If we run the query, we should see that no documents are matched.

That’s because we are searching for the term “that,” which is a stop word, and

hence it was removed when indexing the document.

In other words, that’s to be expected.

Let’s now actually get to updating the analyzer.

We do that with the Update Index Settings API as you have already seen.

We just specify the full analyzer as if it didn’t already exist.

In the query that you see on your screen, I have removed the “stop” token filter,

because I no longer want to remove stop words.

As when adding a new analyzer to an index, we need to close the index before we can modify

the analyzer.

Now that the index has been closed, we are ready to update the analyzer.

That was the only modification we needed to do, so let’s reopen the index.

Just to prove to you that the analyzer has indeed been updated, let’s quickly retrieve

the index’ settings.

Indeed we can see that the analyzer no longer contains the “stop” token filter.

Let’s now index another document with the same value as the first one, so I will just

run the same query again.

Running the search query again should give us a different result compared to the first

time we ran it.

This time around one document is matched, being the one we just indexed.

What happened was that the second document was indexed with the updated analyzer which

did not remove any stop words.

As a result, the inverted index for the “description” field does contain the term “that,” unlike

for the first document.

Hopefully that makes sense, but it also raises a red flag.

Our index now contains documents that were analyzed in two different ways, which can

lead to a number of issues.

Search queries will use the latest version of the analyzer by default, but some documents

were analyzed using the old version.

That can lead to quite unpredictable search results.

In this example, two documents contain the same value for the “description” field

within the “_source” object, but only one matches the query.

That’s probably not what you would expect if you were unaware that the analyzer had

been changed after indexing documents.

We therefore need to handle this to avoid causing ourselves some massive headaches.

We could reindex documents into a new index, causing them to be analyzed with the new analyzer.

There is a simpler approach, though; using the Update By Query API, which you hopefully

remember from earlier in the course.

Besides updating documents with a script, this API can actually be used for a different

purpose as well; reindexing values.

That’s what we need in this case, because we want the first document to be reindexed

with the updated analyzer in place.

We can simply do that with the last query that I have prepared.

It reindexes all documents since no “query” parameter is specified.

It’s possible to limit the scope of the query to specific documents, but we don’t

do that now.

Let’s run the query.

The results state that two documents were updated.

Let’s now run the search query again.

And there we go!

Both documents now match the query, because the first document that we indexed has now

been analyzed using the updated analyzer.

Apart from simply updating an analyzer, we took a quick detour to also discover a potential

problem with doing so.

I wanted to show you that, because if we are not careful, updating analyzers can cause

us all kinds of trouble.

Always try to get your analyzers right the first time, i.e. before indexing documents.

Sometimes it is perfectly valid to have to change the configuration of an analyzer, though.

You just need to handle the situation appropriately and everything should be fine.