---
id: 0p5sysia67gs44oh25zpk6v
title: 87_QueryNestedObjects
desc: ''
updated: 1701194297542
created: 1701109694513
---
Let’s now talk about something a bit different. When we talked about field mappings,

I also mentioned the nested data type and how it enables us to query objects independently.

Don’t worry if you don’t remember the details of it, because we will recap the concept along the way.

Otherwise feel free to revisit the lecture named “Overview of data types.”

First we need a different data set to work with. I have prepared the cURL request for

the Bulk API in advance since we have already covered how to import data.

You can find the command within the GitHub repository if you want to follow along.

Just so you know which kind of documents we will be working with, here is an example document.

Each document represents a food recipe. Most of the fields should be pretty self explanatory,

so let me focus on the ingredients field, since that is the one we will use in this lecture.

This field is an array of objects, each representing an ingredient.

These consist of a name along with the amount and unit, such as grams.

The amount is an integer and the unit is one of the values that you see to the right.

Both the amount and unit fields are optional and do not exist for all ingredients.

Alright, let’s head over to Kibana and query the data.

I have a bool query prepared in advance. What I want to do is to

find recipes containing parmesan with an amount of at least 100.

There is nothing you haven’t seen before, so let’s go ahead and run the query.

We can see that six recipes match our criteria.

Let’s search for “parmesan” and go through the recipes one by one.

The first ingredient we see doesn’t have an amount or unit. That’s a bit strange since our query

specified that the amount should be at least 100, isn’t it? Let’s check the other matches.

This one does contain an amount,

but the value is only 60, which is of course below our requirement of 100.

The next one also doesn’t contain an amount.

And this one also has a value below 100.

The same for this one.

The last one contains an amount of 175, so we would expect this one to match.

In fact, we should only see this recipe within the results,

because the others don’t match our criteria. So why are they included then?

To answer that question let’s first inspect the mapping.

Since we didn’t add any explicit field mappings before importing our test data,

Elasticsearch generated dynamic field mappings for us automatically. If we take a look at the

ingredients field, we can see that it was mapped as an object field. We can tell

based on the nested properties key, which is an implicit way of specifying the object data type.

So the field is mapped as the object data type. Every Elasticsearch field may contain

one or more values, so we are essentially dealing with an array of objects in this case.

Earlier in the course we discussed the behavior of arrays of objects, so let’s quickly recap on that.

Internally, the values for each object key are grouped

together and indexed as an array. Consider the following example.

This is not exactly how things are stored, but this is how it works conceptually.

What happens if we query one of the fields is that all of the values are searched.

This is exactly the reason for the unexpected query results that we just saw.

Let’s see why the query matches one of the recipes.

The match query clause is highlighted in blue. It searches through all of the values and matches if

at least one of them contains the term “parmesan.” That’s indeed the case, and so the recipe matches

this query clause. The range query is highlighted in red and matches if an amount is greater than or

equal to 100. In this case there are two values that satisfy this constraint, being 225 and 450.

The important thing here is that these numbers belong to other ingredients that are not parmesan!

The query clause searches through all of the field’s values, regardless of which

object they belong to. Our bool query is essentially asking two questions when

evaluating this recipe. The first is “Does this recipe contain parmesan?” Yes it does.

The second question is “Does it contain an amount greater than or equal to 100?” Yes it does.

That’s why the recipe matches, even though the matching values belong to different ingredients.

What we meant to do was to ask the question

"Does this recipe contain an amount of parmesan of at least 100?”

This all happens because when indexing an array of objects, the relationship between

object keys are not maintained, and so we cannot run such a query on the objects.

Technically we can, but we just won’t get the expected results.

To solve this, we need to do two things; map the ingredients field as the nested data type,

and use a special query. To do this, we need to reindex the documents into an index that

contains the updated mapping. The easiest way is just to delete the recipes index

and create it with the desired field mappings, so that’s what we will do.

I have the mappings prepared in advance just so you don’t have to watch me type them out.

The relevant part here is really just the ingredients field mapping.

I could just as well have left out the other fields and let Elasticsearch map them dynamically.

We can also leave out the mappings for the sub fields — or properties — and let Elasticsearch

map those dynamically as well. All that matters is that we set the type parameter to nested for the

field. This instructs Elasticsearch to index the objects in such a way that relationships

between object values are maintained, meaning that the objects can be queried independently.

Internally, Elasticsearch indexes each nested object as a separate hidden document. A Lucene

document to be specific. That’s the reason we have to query them in a specific way.

So if a recipe contains 10 ingredients, Elasticsearch will actually index 11 documents;

the parent document and a hidden document for each of the 10 ingredients, being the nested objects.

Anyway, let’s create the index with the new mapping.

Now that the mapping is in place, let’s index the recipes again.

There we go. Let’s head back to Kibana.

Before showing you how we can query nested fields, I want to run our bool query again.

This time we didn’t get any matches. That’s because we cannot query fields

of the nested data type like this. While we don’t get any errors,

this is still better than getting confusing and potentially misleading results. Needless to say,

though, we do need to pay attention when querying arrays of objects, because it’s

easy to get fooled. Both when a field is mapped with the nested data type and when it isn’t.

Alright, now it’s time to see how we can actually query nested fields.

We do that by using a specialized query called nested. Let me begin typing out a new query.

First we need to specify the path to the nested field, relative to the root of the document.

In this case that's just going to be ingredients.

In our case the field is at the root level, so we just enter the field name.

If the field were nested within another field,

we could have used the dot notation that you know from field mappings.

Next, we need to specify the search constraints within a parameter named query.

Within this object, we simply add a search query that we want to run on the nested

objects specified by the path. There is really nothing special about this query,

since everything in regards to the nested data type is handled for us automatically.

This means that we can simply drop in our existing bool query.

Note that even though we specified the ingredients field as the path,

we still need to include it within our query by using the dot notation.

That’s all we need to do, so let’s run the query and see what happens.

This time we only got a single match. If you remember the previous search results,

this is actually what we expected in the first place.

Let’s find the parmesan ingredient and verify that everything looks good.

As you can see, the amount is indeed greater than or equal to 100, so that matches the constraints

that we specified within our query. By using the nested data type and query, we therefore applied

multiple constraints to the same nested object, which is what we set out to do in the first place.

Note that even though the nested query defines constraints for the nested objects,

the query always returns the root document when the query matches.

The last thing I want to briefly talk about is how the nested query handles relevance scoring.

In particular, how matching child objects affect the parent document’s relevance score.

Since each nested object is actually a special kind of document internally,

Elasticsearch calculates a relevance score when they match a query. How the parent

document’s relevance score is calculated can be configured with a parameter named score_mode.

By default, the average relevance score of matching child objects is

used for the parent document. So if four nested objects match the nested query,

the average of these four relevance scores is used.

We can also choose to use the minimum or maximum relevance score of the matching child objects,

or add all of them together and use the sum.

Last but not least, we can choose to ignore the

child objects in terms of relevance scoring and use a value of zero.

There is a bit more to nested objects than this, so let’s continue in the next lecture.