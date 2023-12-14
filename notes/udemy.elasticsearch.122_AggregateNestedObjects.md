---
id: 3x368jxfpvas1k186u8ygwp
title: 122_AggregateNestedObjects
desc: ''
updated: 1701195160845
created: 1701193182545
---
Instructor: Remember the nested query

that we used to query fields map with the nested data type?

Similar to that, there is a nested aggregation.

I'll reuse the department index from earlier

because I want to find the lowest age

among all of the employees across all of the departments.

For that purpose, we can use the min metric aggregation.

Consider the query I've prepared in advance.

You might think that does the job,

but let's run it.

Notice how the aggregation produces null

which is clearly incorrect.

As with the nested query

we also need to handle nested fields a little differently

in regards to aggregations.

More specifically, we need to use the nested aggregation

which enables us to aggregate nested documents.

As with the nested query

we need to specify the path of the nested objects.

All right, so let's get rid of this existing aggregation

and add a new one.

I'll name it employees,

and it's of the type nested.

So the path is the path to the objects

as you've seen before, with the nested query.

So in this case, that's going to be employees

because that's an array of objects.

And let's just go ahead and run this

and see the results.

So we can see that we get a bucket

with 15 documents being the 15 employee objects.

Employees from both departments are placed into the bucket

because we didn't specify a query

and a match all query is therefore used implicitly.

We can now use this bucket

with any of the aggregations

that you've seen until now.

In particular, we'll want to use the min aggregation

for finding the lowest age among the 15 employees.

We do that by specifying it as a sub aggregation.

So let's add an aggs key here

with an aggregation named minimum_age

being of the min type,

being a metric aggregation

and the field is employees.age.

As with the nested query,

it's important that you include the path to the objects.

Running this query, we can see

that the youngest employee is 19 years old

and that's how to use the nested aggregation

in combination with the nested field type.

