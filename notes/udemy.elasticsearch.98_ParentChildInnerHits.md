---
id: 2y4uti6fumf67b6gqex6amo
title: 98_ParentChildInnerHits
desc: ''
updated: 1701194528038
created: 1701110041382
---
Instructor: Earlier in this section,

you saw how to use nested inner hits

to see which nested objects cost a document

to match a nested query.

Inner hits can also be used for joint fields though.

So let's take a look at that in this lecture.

The way we do it is exactly the same as you saw earlier,

but the inner hits that are returned depend

on which query we use.

Let's begin by returning inner hits

for the has_child query that you saw in an earlier lecture.

I've already pasted in the query.

So all we need to do is to add the inner_hits option

to it and run it.

So let's go ahead and do that.

So I'll add the inner_hits option here

and add an empty object as the value.

And let's go ahead and run it.

This query returns departments

that have employees matching the defined query.

When including inner hits, we're now able

to see which employees cost each department

to match the query.

For this particular query,

an employee named Daniel Harris matched the query

that we specified, causing the has_child query

to match the Development departments.

The approach is the same for the has_parent query.

So let's click to see it in action.

So I have that query down here.

So I'll just go ahead and add the inner_hits option to it,

exactly as with the above query and run it.

This query matches child documents

that have a parent that match the query

that we have defined.

By including inner hits within the results,

we can now see which department cost each employee to match.

That is, we can tell which parent document,

cost a given child document to be returned.

Given our query, the inner hits

is unsurprisingly the Development department.

But in a more real world scenario,

the query would be one

that could match multiple departments.

Nevertheless, I'm sure you get the point.

And that's all there is to returning inner hits

for queries that use joint fields.