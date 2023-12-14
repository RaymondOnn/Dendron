---
id: 7strf52ntusocznkyhykdgk
title: 83_QueryWithBooleanLogic
desc: ''
updated: 1701194225038
created: 1701109609592
---
Now that you know what compound queries are, let’s take a look at one of them — one that you

will find yourself using a lot. Namely the bool query, with “bool” being short for “boolean.”

As its name suggests, this query is used in situations where you have more than one condition.

Each condition is expressed as a so-called clause, which is just another word for query in this context.

In other words, the bool query contains other queries, which is what makes it a compound query.

These clauses are defined within occurrence types, which are used to define our boolean logic.

I know this probably doesn’t make much sense yet,

so let’s just get right to it and begin writing some queries.

One of the so-called occurrence types we can add is called must,

containing zero or more query clauses.

Within this array, we can add queries that must match. These can be any queries,

including the ones you have seen thus far. Let’s start out simple by matching documents

that contain the “Alcohol” tag. That’s a job for the term query, so let’s type that out.

Running this query, we should only see documents containing this tag.

Alright, that looks as expected. In case you are familiar with SQL,

we have basically constructed a query that is equivalent to the following.

While we could add more clauses within the must occurrence type,

let’s move on to the next one, being must_not.

I’m sure you won’t be shocked to hear that this

is where we can add any query clauses that must not match.

In other words, any document that matches any of these clauses will not be part of the results.

Let’s expand on our query by excluding documents with the “Wine” tag with a simple term query.

This query will match documents with the “Alcohol”

tag that don’t also have the “Wine” tag. Let’s check out the results.

Scrolling through the results, we can find products such as beer, rum, schnapps, liqueur,

etc. Basically a variety of alcohol products that are not wine.

This query basically corresponds to the following SQL query.

Alright, so far so good. With this, you can already build fairly powerful queries,

but the bool query has more to offer, so let’s continue.

The next occurrence type we will cover is named should, which is actually a cool one.

Clauses within this occurrence type are not required to match. If they do,

however, they will boost the relevance scores of the matching documents.

Let’s expand on our query once again.

We are currently searching for any alcohol products that are not wine. Suppose that

we are particularly interested in beer, for whatever reason. Perhaps we just like beer.

Therefore we might want to score beers higher than other kinds of products, without excluding them.

We can do that by writing a query clause that matches beers.

Fortunately we have a tag for beers, so we can simply write a term query for that as well.

Let’s see what that gives us.

Scrolling through the first handful of results, we can see that they are all beers,

after which other alcohol products follow. Awesome, that’s exactly what we were looking for!

We can even expand on this to make the query even better. While all beers should be tagged

as a beer, the term may also appear within other fields, such as the name or description fields.

If this is the case, this should signal an even higher relevance to our search query,

and so we might want to further boost the relevance scores of such documents.

Let’s begin by adding a match query for the name field. This time around we are

using a full text query because we are now searching through unstructured text.

I am just going to do the same thing for the description field as well,

even though our data set doesn’t really contain any meaningful product descriptions.

We could also have added a multi_match query here instead of the two match queries. That

would just yield slightly different relevance scores due to how that one works internally.

The point is just that you can really add whatever query clauses that make sense for your use case.

Anyway, let’s go ahead and run this query.

Inspecting the results, notice how the top results all have the term “Beer” within their name field.

That’s because these documents received a relevance boost from the match query

within the should occurrence type. This is evident when looking at the document

with the name of “Heineken.” This is a beer, but it doesn’t have the term

“beer” within its name field, hence why it is scored lower than the ones that do.

Following that document are the ones that are not beers at all.

Let’s illustrate how this works before moving on,

because visualizing things often makes them easier to understand.

To the left is the search query that we have just written.

To the right are three documents, which are the same documents that

you saw within the query results a moment ago. They all match the query,

so let’s take a look at how Elasticsearch ranks them in terms of relevance.

First, we have the must occurrence type in which we are requiring documents to have the

“Alcohol” tag. While this is a term level query, Elasticsearch does calculate relevance scores for it.

I will get back to this towards the end of the lecture. Let’s see what the scores look like.

Don’t worry about the scores being slightly different. I can’t explain the reason without

getting deep into how relevance scoring works, and this is not the right time to do that.

Next, let’s look at the should clauses,

with the first one looking for the term “Beer” within the tags.keyword field mapping.

Only two of the documents contain this tag, and the relevance scores are as follows.

The second query clause is a full text query that looks for the term

“beer” within the name field. Only a single document matches this criteria.

None of the documents match the query clause that searches the description field,

so this has no effect on the documents’ relevance scores.

The relevance scores for each query clause are then added together,

giving the following scores for the documents.

This includes any matching should clause that may boost a document’s

relevance score. Hopefully that makes sense.

There is one thing you should know about the should occurrence type.

In situations where a bool query only contains should clauses, at least one of them must match.

That would be the case for this query, for instance.

If neither of the query clauses match, a document won’t be part of the results.

In the case that only a single should clause was present, that one would be required to match.

This is convenient in situations where you don’t really care exactly what matches,

but only that something does - and you want to “reward” documents that match. If this behavior

were not in place, you could get very irrelevant results back when no query clauses match.

For queries that specify at least one query clause for the must, must_not,

or filter occurrence types, no should clause is required to match. In this case,

any should clauses are exclusively used to boost relevance scores and not to filter documents.

That’s the case in this query, where the only requirement for documents

is that they contain the “Alcohol” tag. Documents get a relevance boost

if one or more should clauses match, but they are not required to do so.

This behavior can actually be configured with a parameter named minimum_should_match.

If we add the parameter to this query,

then both the must clause as well as either of the should clauses must match.

It’s also possible to specify other values for the parameter, such as a percentage,

but I will let you explore that within the documentation in case you need it.

Usually you won’t need to use this parameter, since most queries do specify at least one

criteria for documents to match. The behavior is just good to be aware of,

and sometimes specifying only should query clauses can make sense.

Let’s now talk about the last occurrence type named filter. Query clauses within this occurrence

type must match. So what’s the difference between the must and filter occurrence types then?

The main difference is that filter clauses ignore the relevance scores of matching documents.

Let’s use our first query as an example.

As you can see, Elasticsearch calculates relevance scores for matching documents, even though they

are the same. Suppose that we moved the query clause to the filter occurrence type instead.

This causes relevance scores to be disabled for the query clause. In this example that makes sense

because we probably don’t care about relevance scoring since we are basically just filtering

out documents that don’t have the “Alcohol” tag. Placing query clauses within the filter

occurrence type is therefore useful if you don’t want them to affect the relevance scoring of

matching documents. An added benefit is that doing so improves the performance of the query, because

Elasticsearch doesn’t need to calculate relevance scores and order the results. This in turn means

that Elasticsearch can cache the results and use them for subsequent executions of the query.

Alright, now that we have covered all of the occurrence types,

let’s take a short moment to recap.

must clauses are required to match and will influence the relevance scores of documents.

Place criteria that documents must fulfill here if you want them to contribute to

matching documents’ relevance scores. Otherwise you should use the filter occurrence type,

which does the same thing except that relevance scores are ignored.

A bonus of that is that query clauses can be cached for future queries.

must_not is used to filter out documents, and so the query clauses must not match.

They don’t affect relevance scores and may therefore be cached by Elasticsearch.

Lastly, each matching should clause boosts the relevance scores of documents. This is useful

for queries that are not required to match. The idea is that the more that match, the better.

The minimum number of clauses that are required to match can be

configured with the minimum_should_match parameter as we previously discussed.

Here is a different overview showing the same thing.

Please pause the video if you would like to have a closer look at it.

I’m just going to continue since it doesn’t contain any new information.

Let’s talk about the match query for a moment. You might be wondering why I want to talk about

it in the context of the bool query. There is a very good reason for that; match queries are

actually translated into bool queries under the hood. Consider the following query, for instance.

This query is parsed by Elasticsearch and the value we are searching for

is analyzed with the name field’s analyzer. Nothing new about that.

What’s new is that Elasticsearch translates this into the following query internally.

Exactly what the bool query looks like depends on a number of factors such as the number of terms,

which parameters are present, etc. In this case a query clause is added to the must occurrence type,

because there is only one term and so it must be present within documents.

Notice that it’s a term query. This might be confusing at first, because I told you that

you should use term level queries for exact matches, i.e. for the keyword data type.

In this case a term level query is used to query a full text field. So what’s the deal here?

The reason this makes sense is because this bool query is constructed based on the output

of the analysis process. This means that the term you see here is what the term looks like

after the analysis process. If you compare the original query to the translated one,

you can see how the term was lowercased as part of this analysis process.

The inverted index for the name field contains terms that have been analyzed.

Now we are querying terms that have been analyzed in the exact same way.

We are therefore comparing values that are in the same format, and that’s why this all makes sense.

If you were to run these two search queries, you would get the exact same results.

Let’s quickly see what a match query containing multiple terms would be translated into.

Since the match query doesn’t require all terms to be present within documents by default, these are

not added to the must occurrence type. Instead, they are added to the should type. Remember that

since only should clauses are present, at least one of them is required to match.

Had we set the operator parameter to “and”,

a term query would be added to the must type for each term instead.

Like I said before, how a match query is translated depends on a number of factors.

There are also cases where it’s translated into phrase or prefix

queries, but it will usually be a bool query.

This was just a bit of background knowledge. The match query is really convenient,

because we don’t want to handle all of this ourselves. Apart from that, it also handles

a number of other things for more advanced queries such as synonyms or fuzzy searches.

So the purpose of showing you this was not for you to replace match queries with bool queries,

but just to give you an insight into how it works under the hood.

Alright, let’s round off this lecture with a couple of example queries.

You can find them within the GitHub repository if you want to run them.

For the first example, let’s find products that have 100 or less items in stock.

On top of that, they should be tagged with “Beer”

or contain “Beer” within their names. This can be expressed in SQL as follows.

If you are not familiar with SQL, I hope it’s still fairly easy to understand the syntax.

Let’s translate this piece by piece, beginning with the in_stock criteria, which is a job for

the range query. I will add it within the filter occurrence type because this constraint shouldn’t

have any effect on relevance scoring, and doing so makes the query a candidate for caching.

Next, we have two criteria where at least one of them must match.

The Elasticsearch way of using the OR boolean operator is to use the should occurrence type.

However, adding two query clauses there makes them optional, which is not quite what we are looking for.

Instead, we can actually add a nested bool query within the must occurrence type.

Within each occurrence type, we can define any query clause that we want,

so there is nothing stopping us from nesting a bool query as follows.

Since this nested bool query contains no must or filter occurrence types,

at least one of them is required to match, which is exactly what we were looking for.

And that’s it, this query should correlate to the SQL WHERE clause at the top.

There is nothing wrong with adding a nested bool query. In fact, it may be required when

constructing complex search queries. However, when doing so, we should consider if it’s possible to

simplify the query, both in terms of performance and readability. In this example we can actually

be smart and use the minimum_should_match parameter. Consider the following query.

By using this parameter, we can get rid of the nested bool query and instead require at

least one of the query clauses to match. This effectively gives us a boolean OR operator.

The performance difference between the two versions is probably limited,

but the second approach is easier to understand. It’s also more explicit, since the first version

requires anyone reading it to know that at least one of the should clauses are implicitly required.

Alright, let’s move on to another example.

This one should match products with the “Beer” tag that have 100 or less items in stock.

On top of that, “Beer” should appear within the

product name or description. This can be expressed in SQL as follows.

The constraint for the in_stock field can be

translated in the same way as before, so nothing new here.

The tag constraint can be translated into a term query. Since we don’t really need

it to affect relevance scores either, we can add it to the filter occurrence type.

So far so good.

Next, we need to handle the boolean OR, which means we should use the should occurrence type.

Just like before, we use the minimum_should_match

parameter to require at least one of the clauses to match.

If we wanted to, we could replace the two should clauses with a single query. Perhaps

you know which one? Exactly, the multi_match query! Since this query matches if at least

one of the specified fields contains the term, we can add this within the must occurrence type.

This also means that we can remove the minimum_should_match parameter.

Note that these two queries are not 100% the same,

since the multi_match query behaves slightly differently in terms of relevance scoring.

We covered that a bit earlier, so hopefully you remember that. Not that it’s important now anyway.

Those were just a couple of examples. For each of them, I wanted to show you

how there are often multiple ways of doing the same thing, which is just a testament to how

powerful and flexible the bool query is. You will probably find yourself using this query a lot,

so hopefully you feel fairly confident with it. Otherwise I encourage you to rewatch

this lecture if necessary and play around with the query within the Console tool.

Anyway, that’s it for this lecture, so I will see you in the next one.