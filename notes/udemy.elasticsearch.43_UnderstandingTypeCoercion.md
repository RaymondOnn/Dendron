---
id: ison8ad6meild3dwcni4l1i
title: 43_UnderstandingTypeCoercion
desc: ''
updated: 1701104582100
created: 1701085143771
---
Now that you know about the most important data types, I want to tell you about something

called type coercion.

As you know, Elasticsearch checks the data types for each field value when we index adocument.

A part of this process is to validate the data types and reject field values that are

obviously invalid.

An example of that would be trying to index an object for a text field.

Sometimes we can get away with providing the wrong data type, though.

Let’s head over to Kibana so I can show you an example instead of just talking about it.

Here I have three queries prepared, which index three documents into a new throwaway index.

We haven’t created this index, so it will be created automatically when indexing the

first document as I told you earlier in the course.

Let’s run the first query.

The index has now been created and the document has been indexed into it, so no surprises

so far.

A mapping for the “price” field has actually been created due to dynamic mapping, which

you will learn more about soon.

What Elasticsearch did, was to inspect the value that we supplied in order to figure

out its data type.

In this case we supplied a floating point number, so the “float” data type was used

within the mapping.

As for the next query, you have probably noticed how the floating point number has been wrapped

within double quotes, effectively turning it into a string value.

What do you think will happen when we run the query?

Well, let’s try.

The document was indexed correctly even though we supplied a string value instead of a floating

point number.

This is where type coercion comes into the picture.

Elasticsearch first resolves the data type that was supplied for a given field.

In the case where there is a mapping for the field, the two data types are compared.

Since we supplied a string for a numeric data type, Elasticsearch inspects the string value.

If it contains a numeric value — and only a numeric value — Elasticsearch will convert

the string to the correct data type, being “float” in this example.

The result is that a string of 7.4 is converted into the floating point number 7.4 instead,

and the document is indexed as if we had supplied a number in the first place.

So what happens if we supply a string containing something that cannot be converted to a float?

Well, I’m sure you can imagine, but let’s try.

As you can see, the query failed because the data type was incorrect, and Elasticsearch

was unable to coerce the value into the appropriate data type.

Let’s retrieve the second document that we indexed, because I want to show you something.

Notice how the value within the source document is a string and not a float.

Didn’t I just tell you that Elasticsearch coerces the value into a float instead of

a string?

I did, but there’s actually a good explanation.

The reason is that the “_source” key contains the original values that we indexed.

These are not the values that Elasticsearch uses internally when searching for data.

Can you guess where I’m going with this?

Elasticsearch searches the data structure used by Apache Lucene to store the field values.

In the case of text fields, that would be an inverted index.

In this example, the value has been converted into a float, even though we see a string

here.

Within Apache Lucene, the value is stored as a numeric value and not a string.

This means that if you make use of the value within the “_source” key, you need to

be aware that the value may be either a float or a string, depending on the value that was

supplied at index time.

If this is a problem for you, you either need to supply the correct data type in the first

place, or disable coercion, which I will show you how to do a bit later.

In this lecture, you saw how coercion works in the case of converting strings to floats.

Another example of coercion could be to supply a floating point for an integer field, in

which case the float will be truncated to an integer value.

One important thing to mention is that coercion is not used when figuring out which data type

to use for a field with dynamic mapping.

This means that if you supply a string value, the data type will be set to “text” even

if the string only contains a number.

In general you should always try to use the correct data types.

It’s especially important when you index a new field, to ensure that the correct data

type is used in the mapping — provided that you make use of dynamic mapping.

Anyway, the point is that Elasticsearch does not use coercion when creating field mappings

for us; it’s only used as a convenience that forgives us if we use the wrong data

types.

Whether or not you should disable coercion is a matter of preference.

I personally prefer to disable it, just to ensure that Elasticsearch always receives

the data type that it’s supposed to; otherwise I prefer an error to be thrown so that it

can be fixed.

Coercion is enabled by default to make Elasticsearch as easy and forgiving as possible, but you

will see how to disable it a bit later.

The last thing I will do in this lecture, is to get rid of the index that we just created,

since we won’t be using it again.