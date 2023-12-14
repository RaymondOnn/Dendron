---
id: kq7k2v9gfgxfxd80064iylz
title: 24_ReplacingDocuments
desc: ''
updated: 1701104022357
created: 1701080736621
---
You have now seen how to index new documents and how to update existing ones.

In this lecture, I will show you how to replace documents.

Fortunately that's super easy, so this is going to be a quick one.

Especially because you have already seen the query needed to perform a document replacement.

Because of that, I have already pasted it in to save a bit of time.

Remember when we used that query?

We used it when we indexed a new document with a specific ID.

That's actually the same query that we can use for replacing existing documents.

Let's retrieve the current document that has the ID of 100.

As we can see, since indexing the document, we modified the "in_stock" field and added

a new "tags" field.

Let's try to change the "price" field and replace the document altogether.

Let's retrieve the replaced document.

We can now see that the document contains exactly the fields that we just specified.

This also means that the "tags" field is now gone, because the whole document was

replaced with the document that we supplied to the query.

That's how easy it is to replace documents with Elasticsearch.