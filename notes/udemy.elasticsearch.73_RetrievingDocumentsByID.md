---
id: q51eu4pqe0f7dypi788jdkd
title: 73_RetrievingDocumentsByID
desc: ''
updated: 1701193953440
created: 1701105955304
---
Earlier in the course I showed you how to retrieve a single document based on its identifier.

In this short lecture, I want to show you how we can use a term level query to retrieve multiple documents at

once based on their IDs. In both cases we are querying the value of a document’s _id field.

The name of the query is ids, so let’s add that to the partial query that I have prepared.

We then specify the IDs as an array for a values parameter.

Let’s retrieve three documents in this example.

That’s all, so let’s run the query.

Within the results, we can see that three documents matched

the query. Those are returned within the hits key.

Note that not all IDs are required to match. The query simply returns any documents that

are found with the specified IDs. So if you supply ten IDs for the query,

the results will contain between zero and ten documents.

In situations where you don’t know for sure that the IDs exist within your index, you may

want to compare how many documents are matched by the query with how many IDs you supplied.

So that’s how to retrieve multiple documents at once based on a number of identifiers.

This could be useful if you are using the same identifiers for your Elasticsearch

documents as within a relational database, for instance. The ids query then makes it

easy to retrieve specific documents because you already know their IDs.

Anyway, that’s all for this lecture.