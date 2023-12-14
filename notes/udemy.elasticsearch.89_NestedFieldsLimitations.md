---
id: zqfnlto7o0kh5u6qyuk4pry
title: 89_NestedFieldsLimitations
desc: ''
updated: 1701194334665
created: 1701109739213
---
Alright, let’s finish talking about nested fields for now by talking about a few

limitations you should be aware of. I will try not to take up too much of your time,

because there is no guarantee that you will end up even using nested fields.

First of all, indexing and querying nested fields can be expensive in terms of performance. As you

know, Elasticsearch creates an Apache Lucene document for each nested object.

If you index a million documents each containing just ten nested objects, you will end up with

11 million documents. Your index will still show one million documents since the Lucene

documents are hidden and handled internally. Naturally this behavior comes with a cost.

Elasticsearch is generally super fast, so unless you are dealing with large amounts

of data and query throughput, you probably won’t notice any performance penalty, though.

The more you scale things up, the more likely you are to see degrading performance and run into problems.

To reduce the risk of that happening,

Elasticsearch provides a couple of safeguards, so let’s quickly look at those.

The first thing I want to mention is perhaps not a limitation as such.

We need to use specialized queries when working with nested fields. But as you saw,

the nested query is super flexible and enables us to nest any normal query within it,

so it doesn’t really limit us in any way; it just requires us to write queries a little differently.

There is a limit for how many fields with the nested data type that can be added to an index.

It defaults to 50, which should be plenty, so it is unlikely that you will ever run into this limit.

Should that be the case, though, this is a setting that can be increased, provided

that you understand that you are probably mapping your documents incorrectly if you need to do this.

Another limit is that a document may contain a maximum of

10,000 nested documents. This number is across all nested fields within a document.

This limit is in place to help prevent against out of memory exceptions which might otherwise occur.

Increasing this limit is possible, but generally not recommended.

Suppose that we are running a service similar to Shopify where we provide webshops for companies.

We could then index each webshop into a webshops index. Logically speaking, it would be natural

to store the orders for each webshop within an orders field, mapped as a nested field.

If we are just looking at the JSON object, this makes perfect sense.

But if we consider the performance implications of storing things like this within Elasticsearch,

it surely doesn’t make sense. We have two nested fields; the orders field and then a nested lines field.

The number of objects between the two fields count towards the 10,000 nested objects limit.

It’s just a matter of time before a customer hits 10,000 orders.

In fact, it’s even less since each order contains at least one order line. This is clearly not a good design.

An alternative would be to split the data into two indices; one for webshops and one for orders.

This is a much better design because the limitation of 10,000 nested objects now

only applies to the lines field within the orders index. The number of objects within

a document doesn’t grow over time, because once an order is complete, no new order lines

are added to it. And no order is ever going to exceed the limit or even get close to it.

So when mapping your documents and deciding to make use of nested fields, just keep this

in mind. Is it realistic that a document will ever have thousands of nested objects?

If so, it might be worth reconsidering how you map your documents.

That’s all I wanted to cover in this lecture. If you keep these limitations

in mind when mapping your documents, then you should be absolutely fine.