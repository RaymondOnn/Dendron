---
id: zlov8vsvlveb4fo354haexz
title: 88_NestedInnerHits
desc: ''
updated: 1701194316509
created: 1701109712670
---
You just saw how to map and query arrays of objects independently of each other.

When using the nested query, it returns the parent document whenever a nested object matches.

In our case it meant that searching for ingredients returned a result set containing recipes, being

the root documents. That’s how Elasticsearch works in general, and that’s fine. But apart from just

knowing that a recipe satisfied our criteria, we might also want to know what caused it to do so.

In particular, which nested objects matched. We can do that by using something called inner hits.

Inner hits can be useful for a number of use cases, one of which is highlighting

matches. Let’s revisit the query from the previous lecture and see how this works.

Enabling inner hits is extremely easy,

so all we need to do is to add a parameter named inner_hits to the nested query.

The value should simply be an empty object, at least when using the default behavior.

That’s all! Let’s run the query and check the results.

At the bottom, we can see the inner hits. Let’s talk about this additional data that

was added. It can be a bit overwhelming at first, so let’s go through it step by step.

Here is the overall structure of search results, with the actual hits omitted.

And here is an example of a hit, being a document along with some metadata.

The document itself is available within the _source key,

which is also nothing new. So far so good.

When specifying the inner_hit parameter, a new object is added for an inner_hits key as follows.

This object contains information about inner hits and is added at the same

level as the _source object, i.e. at the root of each hit.

Within this inner_hits object, an object is added for each field for which we have inner hits.

In this example we searched the nested objects within the ingredients field. By default,

the key is named the same as the path specified within the nested query. Within this object we

have a hits object, which has the same structure as the outermost object of the same name.

Let’s look at the actual hits a bit further down the hierarchy.

These hits are the inner hits, being the matching nested objects for the ingredients path.

The _nested key contains information about the nested object that was matched.

Most interesting is the offset key which defines the nested object’s offset within

the parent document’s _source object. You can also think of this as the index within an array,

which you may be familiar with from various programming languages.

Note that the offset begins at zero. Inner hits are sorted by their relevance scores by default,

so the order in which they appear may differ from the one within the document’s _source object.

The offset therefore tells us where the inner object appears within the _source object.

Alright, I know what you are thinking by now. This JSON object is getting out of hand.

Yes, I agree with you; it’s a bit of a nightmare to look at, but that’s just how it is.

Fortunately, you will usually interact with search results programmatically,

so you won’t have to look at it very often.

Anyway, here is the JSON object shown with dot notation, which is a bit easier to look at.

It’s still fairly ridiculous but hey, don’t blame the messenger! ;-)

Alright, just one thing left to cover; two simple parameters.

The first one simply enables us to change the key that appears directly within the inner_hits

object within the results. It defaults to the path specified within the nested query.

It’s typically not necessary to change it, but it might make sense for complex queries. Why? Because

a query might contain multiple nested queries, perhaps contained within a bool query. And,

these queries may use the same path. So in certain situations it can make

sense to change the name — or if you just prefer to call it something else of course.

The second parameter enables us to configure how many inner hits we want to be returned for

each matching document. By default, up to three hits are returned, sorted by relevance scores.

In our example we increased this to include the top ten inner hits.

There are a few more parameters that you can use,

but they are not frequently used, and so I won’t cover them here.

Alright, that’s it for inner hits. With the nested query, we can query nested objects

independently of each other. That’s great for filtering the objects, but sometimes we might

want to know why a given object matched the query. That’s what we can do with inner hits.

I’ll see you in the next lecture.