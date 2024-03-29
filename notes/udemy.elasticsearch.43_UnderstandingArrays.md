---
id: a5i57cr5il31v77kkiyw0ke
title: 43_UnderstandingArrays
desc: ''
updated: 1701104612543
created: 1701085102006
---

A moment ago we covered the most important data types.

Among those were basic data types such as text, integer, boolean, date, etc.

But what if we want to store multiple values for a field?

Perhaps you noticed that I didn’t mention any “array” data type.

There’s a good reason for that; it doesn’t exist.

That’s because any field in Elasticsearch may contain zero or more values by default.

That’s right - you can index an array of values without defining this within the field’s mapping.

We actually did that in the previous section where we indexed a “tags” field for products.

All we did was to supply an array as the field value, and everything worked out of the box.

This means that both of these two queries are valid, and you can run them in any order.

The “tags” field will be mapped as a “text” field, so we don’t define that the value

may contain multiple values; that’s just how Elasticsearch works by default.

The mapping would just look as follows.

As you can see, there is no sign of an “array” data type or the likes anywhere.

So how is this stored internally, you might wonder?

In the case of text fields, the strings are simply concatenated before being analyzed,

and the resulting tokens are stored within an inverted index as normal.

We can verify that this is the case by using the Analyze API.

Here I have a simple query that specifies an array of two strings instead of just a

single string.

To the right, we can see that the results contain tokens from both strings.

Notice the character offsets for the last two tokens, originating from the second string.

The offsets don’t start over from zero, but rather continue from the last offset of

the first string.

I hope that makes sense.

This means that the strings are indeed treated as a single string and not as multiple values.

I should mention that the strings are concatenated with a space in-between, because otherwise

the words “simply” and “merged” would end up within the same token.

In the case of non-text fields, the values are not analyzed, and multiple values are

just stored within the appropriate data structure within Apache Lucene.

Anyway, enough about how the values are stored.

There is one constraint when using arrays that you should be aware of; all of the values

within the array must be of the same data type.

This means that you cannot mix strings and integers together in the same array, for instance.

Well, I said that data types cannot be mixed, but that is not 100% true.

You can mix data types together as long as the provided types can be coerced into the

data type used within the mapping.

That’s of course assuming that coercion is enabled.

Consider the following arrays, for example.

While it’s certainly not ideal, these arrays will work, because the values that have a

wrong data type, can all be coerced into the right one.

That’s usually the case for simple data types, but not for other ones such as objects.

In this example, Elasticsearch has no way of coercing a boolean into an object, and

so it will return an error.

An important thing to note is that coercion of array values is only supported when a mapping

has already been created, either explicitly or through dynamic mapping.

If you index a field for the first time and no mapping already exists, it will be created

automatically with dynamic mapping.

However, in this case you are not allowed to supply mixed data types; that’s only

allowed when indexing documents after the mapping has been created.

I don’t recommend relying on coercion, but if you do, make sure that you provide the

correct data type for all array values until the field mapping has been created.

It’s also possible to index nested arrays, such as the example that you see on your screen now.

Supplying such an array will cause Elasticsearch to flatten it, i.e. to move any nested array

values up to the top level.

As a last thing, I just want to remind you that if you index an array of objects, you

need to use the “nested” type if you want to query the objects independently.

This is just a reminder of what we discussed in a previous lecture.

If you don’t need to query the objects independently, you can just use the “object” data type.

And that’s all there is to arrays, so let’s continue.