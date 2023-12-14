---
id: rnq55rcj9z4wszvm2k842fy
title: 23_Uprserts
desc: ''
updated: 1701104005791
created: 1701080716740
---
Yet another way of updating documents, is by performing so-called upserts.

Upserting means to conditionally update or insert a document based on whether or not

the document already exists.

So if the document already exists, a script is run, and if it doesn't, a new document

is indexed.

To save a bit of typing, I have already prepared the two queries that we will need.

You already know one of them, and the new one should look pretty familiar to you.

The request path remains the same, meaning that we are still using the Update API.

I have specified an ID of 101, because we will be indexing a new document.

As I mentioned before, the script is run if the document already exists; otherwise the

contents of the "upsert" option is added as a new document.

As you can see, I have added the same three fields that we used for the other documents.

While this example honestly doesn't make much sense, I'm sure you get the idea of

how upserts work.

You can of course use parameters for the script if you need to, and write a more complex script.

I'm quite sure you can imagine what will happen when we run this query, but let's

take a look.

Since no document with an ID of 101 already existed, the contents of the "upsert"

option should have been indexed as a new document.

We can verify this by inspecting the query results, and the "result" key in particular.

This key contains a value of "created," meaning that the query caused a new document

to be created.

We can now retrieve the newly created document, just to make sure that it exists and looks

how we expect.

Alright, no surprises here.

Let's now try to run the first query again, being the upsert query.

The value of the "result" key within the query results, is now "updated." That's

because the document already existed this time.

The document was therefore updated instead of being created, so our script was run.

If we retrieve the document once again, we should see that its "in_stock" field has

increased to six.

And indeed that is the case.

That's how to perform upserts with Elasticsearch.