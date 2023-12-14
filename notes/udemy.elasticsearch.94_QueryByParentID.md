---
id: 4zxdcnv5mz5371cqaqbgxw0
title: 94_QueryByParentID
desc: ''
updated: 1701194448368
created: 1701109907883
---
Instructor: Time to write the first query

in the context of document relations.

Lets start off with a super simple query

which returns the child's documents

based on the parent document's ID.

The query is named parent_id

and in our case we can use it to

retrieve the employees for a given department.

So lets add the query object first of all.

So the query is named parent_id

and I'll just add an empty object for now.

First, we need to tell elastic search

which relation we want to query

by adding an option named type.

This is the type of document we want returned

so we should type in the name of the

child relation type here, being employee.

So let's do just that.

So type and employee is the value.

We also need to specify the ID of the parent document

being the department.

We do this by adding an option named ID.

I'll just choose the first department

and then to 1 list the value.

So, ID, and 1.

That's it, lets run the query and see

if we get the expected results.

Within the results, we can see that

the query matches four documents

which is what we expected based on our test data.

If I change the parent ID to 2,

the query should match the employees

in the other department instead.

So lets try to do that.

And I'll run it again.

And indeed we can now see the other employees

within the results.

That's really all there is to this query.

I told you it was easy.

Before ending the lecture,

I just want to mention two things about the search results.

First of all, notice how the hits contain the

join field within their underscore source key.

This is not useful to us in this case

as we know both of the values at query time.

But for other queries it might be relevant

to check the types of the matched documents.

Secondly, notice how the hits also included _routing key

with a value of 2.

This number matches the ID of the parent document.

Remember that a parent document and all of its

child documents must be placed on the same shot

which I mentioned in the previous lecture.

The reason I mentioned routing again is just to show you

that when using the parent_id query

the value that will pass to the ID option

is automatically used as the routing value.

In other words, elastic search automatically uses

the ID of the parent document

to figure out which shot the documents are placed on.

This is just a default behavior

and can be overwritten by using the routing query parameter

if you did some custom routing

but that's not such a common thing to do.

If all of this just went over your head,

then don't worry about it

as its just a bit of background knowledge

that's not essential to understand

when using the parent_id query.