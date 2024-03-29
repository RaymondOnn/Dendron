---
id: qb56we06fey1cca2o5mf683
title: 20_RetrievingDocumentsByID
desc: ''
updated: 1701103938440
created: 1701080664402
---
Let's try to retrieve one of the documents that we just added. We will need to know its

ID, and since I didn't make a copy of the auto-generated ID for one of the documents,

I will retrieve the one with an ID of 100. Since we will be retrieving a resource, we

will use the "GET" HTTP verb. The endpoint will actually be the same as when we added

the document, so this is another example of how the HTTP verb sometimes specifies the

action that should be performed.

Running the query, we can see that Elasticsearch returns the document that we added. The JSON

object that we specified when adding the document, is returned to us under the "_source" key.

If the document is not found, the "found" key will be set to "false," and the "_source"

key will not be part of the results.

That's how easy it is to retrieve a specific document, provided that you know its ID!

Later in the course you will see how to perform searches for documents matching some criteria,

but that's a topic for a bit further down the road.