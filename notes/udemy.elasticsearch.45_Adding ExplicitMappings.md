---
id: jvukh7zebweziwhbyes1ig5
title: 45_Adding ExplicitMappings
desc: ''
updated: 1701104636505
created: 1701085178816
---
We have now covered some basic things about mapping in Elasticsearch, so let’s now get

started actually creating some mapping!

You can add field mappings both when creating an index and afterwards.

Let’s start out defining the mapping when creating a new index, since that’s typically

the first way that you will interact with mapping.

We do that by defining the mappings within a “mappings” key when we add a new index,

so let’s type that out.

All field mappings should be defined within a “properties” key.

That’s the case for fields at every level of the hierarchy, including nested objects

as you have already seen.

So let’s add the “properties” key.

Great, so now we are ready to define the field mappings.

We do that by specifying the field names as keys and add objects as the values.

I’ll add four fields; “rating,” “content,” “product_id,” and “author.”

The “author” field is going to be an object, so that one is a bit special.

For the three other fields, we simply define their data type with a “type” key, so

let’s type that out.

In place of “float,” “text,” and “integer,” you can of course use any of the data types

that you saw earlier.

The “author” field is a bit special because it’s an object.

That’s why we need to add a “properties” key again, just like we did at the root level

of the mapping.

This tells Elasticsearch that it is dealing with an object containing mappings for its keys.

Within this “properties” key, we define mappings exactly as we did at the root level

of the mapping.

In this case I will define three mappings; “first_name,” “last_name,” and “email.”

If you wanted to, you could nest objects even further than this.

As you know, “keyword” fields are used for filtering and aggregations, and for exact

matches.

That’s why I chose the “keyword” data type for the nested “email” field.

When choosing between the “text” and “keyword” data types, it’s important to consider how

the field will be queried.

Will we need to perform a full-text search on the e-mail address?

Probably not.

Instead, it’s much more likely that we will be searching for all reviews left by a user

containing a specific e-mail address.

Or perhaps performing aggregations on the e-mail address to see how many reviews a given

user has written.

An alternative to the e-mail address could be to store the user’s ID as a number.

Alright, we are done with the mapping now, so let’s run the query to create the index.

The index has now been created with the mapping that we defined.

Let’s index a document into the index.

To save some time, I will just paste in the query to do so.

Before running the query, I will just change the e-mail address to an object, just to show

that Elasticsearch will then refuse to index the document.

Indeed Elasticsearch refuses the document because one of the supplied values is invalid

compared to the field’s mapping.

The reason I supplied an object and not an integer, for instance, is that the integer

would just be coerced into a string, and Elasticsearch would index the document as normal.

That’s the default behavior since we didn’t disable coercion.

I’ll just revert the change and run the query again.

The document has now been indexed correctly and is therefore searchable.

That’s really the basics of explicitly adding mappings, at least when creating a new index.

Before wrapping up this lecture, I just want to mention that there is no naming convention

for field names.

As long as you are consistent, you can name your fields however you want.

That being said, though, camelCase and snake_case seem to be the most widely used for JSON,

so I would recommend using one of those.