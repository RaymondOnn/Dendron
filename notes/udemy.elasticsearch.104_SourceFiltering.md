---
id: 7m2fb0mk4lp7bqkoxx1lhzb
title: 104_SourceFiltering
desc: ''
updated: 1701194783522
created: 1701192830623
---

What do you think of the captions?
4:22 / 4:26
between the select and from keywords.
Up next
105. Specifying the result size
Transcript
Instructor: In this lecture, I want to show you

how to control which parts of the _source field

that are returned for each of the matches

for a search query.

By default, the whole contents of the field are returned,

but you might not always want that to be the case.

The motivation for limiting what is returned

for the _source field would usually be

to reduce the amount of data that's returned

and thereby transferred on the network.

So usually, the motivation is to improve performance.

You might not even notice a difference

for small applications,

but for applications with a lot of throughput,

it can definitely be something that's worth tuning,

especially if you have long text fields.

Alternatively, you can disable the _source field altogether

if you don't need it at all.

This is useful if you just want to use Elasticsearch

for searching and finding the IDs of documents,

which you could then use to retrieve the items

from some other data store.

Let's see how to do that, first of all.

So I've added a simple match query in advance,

and all we need to do is to add a parameter

named _source and set it to false.

So we'll do this at the top level of the query,

or rather the request body.

So here, _source, and now specify false as the value.

So let's run the query and see what it looks like,

and as we can see within the results,

there's no _source key for the matches.

We can also specify the name

of one field that we would like to return,

or alternatively, an array of field names.

So let's try that.

I'll try a single field first,

and I will use the created fields.

Let's run it.

And now you can see

that we only get the created field in return.

For objects and nested properties,

we can specify the key path for fields.

So if we just want to include the name field

within the ingredients object,

then we can specify that by writing ingredients.name.

So let's do that.

ingredients.name, like so.

Let's run it,

and now we can see that we only get the name property back

for each object within the ingredients array.

Alternatively, we can make use of wildcard patterns

and match all keys within an object.

So I can add an asterisk right here,

and it will match all properties within the objects

stored within the ingredients array.

Let's run it,

and now we get all of the properties in return.

Like I said earlier,

we can also specify an array of field names.

Let's include all keys within the ingredients object

and also the entire servings object.

So let's define an array here, an array of strings,

and first, specify ingredients.*,

and then the servings field, okay?

So let's run it and check the results.

So we get the entire ingredients object back,

and we'll also get the entire servings object in return.

Last but not least, we can change the _source parameter

to an object with the includes and excludes key

for complete control.

Suppose that we wanted to include all of the keys

within the ingredients objects except the name fields.

Let's define that.

So let's get rid of what we had already

and add an object for the _source parameter instead

with an includes property.

I'll add an S here,

and here, I'll specify ingredients.* to include all fields,

and then add an excludes parameter here

where I'll exclude ingredients.name.

Let's run it,

and now we can see that the name property

is no longer returned for each object

within the ingredients field.

This example doesn't make so much sense

because we only have two keys within the ingredients object,

but I'm sure you can imagine

that it could be useful for objects with many keys.

So that's how you can limit the amount of data

that's returned from a search query.

If you have experience with relational databases,

then you can figure filtering

the underscore source field like this

as the equivalent of specifying which columns

a query should return

between the select and from keywords.