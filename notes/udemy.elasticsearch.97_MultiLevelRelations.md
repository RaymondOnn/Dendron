---
id: bnjhddqstwvive4796pelcd
title: 97_MultiLevelRelations
desc: ''
updated: 1701194510685
created: 1701109998192
---

Instructor: So far we have only been working

with one level of relations

but it's also possible to have so-called multi-level

or nested relations.

In the context of the example

that we have been working with so far,

we could have a company containing departments

which in turn have employees.

But just to avoid having to update the mapping

of our existing index,

I'm going to create a new one for this lecture.

I'll name it company,

and it's going to contain the relations

that I just mentioned along with a supplier relation

that belongs to a company.

That one really has nothing to do with multi-level relations

but that's just to show an example

of one relation containing multiple relation types

which I mentioned earlier in this section.

All right, so let's get started creating the index

and the mapping.

I've prepared most of the query in advance

so we just need to populate the relations object,

which is the interesting part.

First, we need to define the company relation type,

which is going to contain both a department

and supplier relation type.

Hence why the value will be an array.

So let's add a key named company,

whoops, company

and an array as the value being an array of strings.

So first, departments and then supplier.

You saw the same thing earlier,

although at that time we only specified one relation type

as the value.

All right, so all we need to do now is to define

that an employee should be a child of a department.

We can do that by adding another key name department,

matching the child of the company relation type

that we already defined.

So let's do that.

Department and a string value.

So hopefully you can see that the key matches the child

of the company relation above.

The value is going to be the relation type

exactly as you've seen before.

So in this case it's going to be employee.

All right, so let me just recap on what this does

because it can look a bit confusing at first.

We have a company relation which is the parent relation

for both the department and supplier relations.

So a company can have multiple departments and suppliers.

Then we define that a company can have multiple employees

which adds another level of relations

than what you've seen before.

We did that by specifying the name

of an existing relation type

as the key within the relations object

and then just define the child relation type

in the same way as you've seen before.

This leaves us with relations matching the three structure

that you saw on the diagram earlier

and that you can see on your screen right now.

Let's go ahead and run the query

because then I want to show you how to add documents

to the new relation.

All right, so now the index has been created.

Let me scroll down because I have a few queries down here.

So I've prepared the queries that add a company

and their department in advance

because those are exactly the same as the queries

that you saw in the previous lecture.

Let's run these two queries, first of all.

All right, that lets us focus on the important parts,

being to add an employee.

I've prepared this query in advance as well

except for the routing query parameter

and the parent option.

That's just to save a bit of typing

because the rest stays exactly the same

and in fact, so does the parent option,

which should contain the ID of the department it belongs to.

There will be two in this case.

So let's just go ahead and fill that out.

So I'll go down here and set the parent object to two.

What's new is the routing query parameter

because this should no longer be the ID

of the parent document.

In our mapping, an employee is a grandchild of a company.

What we need to do is to specify the ID of the grandparent

of the document,

which is the company that the department belongs to.

In this example, the company has an ID of one

so let's answer that as the value.

Like so.

We're doing this for the same reason as before,

being that we need the document

to be routed to the correct chart

but when using multiple levels of relations

you need to route to this chart that contains the document

at the highest level of the document hierarchy.

All right, and let's run this query as well.

All right, so now that you've seen how to index documents

in the context of multi-level relations,

what about searching for them?

The answer to that question is quite simple.

You do it in the same way as you've already seen,

i.e, by using the queries that you saw

in the past couple of lectures,

namely the has_parent, has_children

and parent_id queries.

They work no differently

in the context of multi-level relations

because they just operate on a relation between two types.

The queries are not concerned

with where in the hierarchy these types are placed,

so they work in exactly the same way.

That being said, you might need to nest the queries

if you want to restrict the matched documents

on multiple relation levels.

So let me give you an example of how that can be done.

Before getting into the example,

I just want to quickly add a few more documents

so I can actually prove that the following query

is going to work.

So I'm going to scroll down a bit

because I have a couple of more queries down here.

So I'm just going to run a couple of queries

that I've already prepared which just add a new company

with a marketing department with an employee named John Doe.

So let's run these three.

Great, now, we have a bit more data to work with.

Suppose that we want to retrieve the companies

with an employee named John Doe.

Since the employee type is a grandchild of the company type

we need to do a bit more work

than just using the has_child query

since we have the department type in between.

So more explicitly we want to find companies containing

at least one department

which has an employee named John Doe.

Let's get started building that query.

The first step is to use the has_child query

for the department type

because we want to find the companies

that have departments matching a given query.

We'll define the query in a moment.

So let's get started typing out the query.

So get company and the search API as always,

query objects, and the has_child query.

So the type is going to be departments.

And now let's get to work on the query objects.

So now we have to define the constraints

for the departments.

Or actually we don't want to put any constraints

on the departments themselves

but rather, their relationship to employees.

So we want to match departments that contain employees

which is a perfect fit for the has_child query once again.

So what we will actually do is to list the has_child query

within a has_child query.

While it may sound and look strange at first,

let's go ahead and type it out

and I'll explain that afterwards.

So I'll add another has_child query here.

I'll set the type to employee

because now we're going to add a constraints

to the employee document type or relation type.

Within this new query object,

we can add constraints to the employees

that we want to match as I just mentioned.

In this case,

I'll just search for the employee named John Doe,

so I use the term, query, and the keyword, mapping.

So let's add a term query here.

So the field is name and I'll use the keyword, mapping,

and specify John Doe as the value.

And that's it.

Before running the query let's take a moment

to recap on what this query does

since I understand that it may look a bit confusing.

The first has_child query finds any company

that contains departments that match the query.

In this case, that query is another has_child query

which finds employees with the name John Doe.

So if the innermost has_child query returns

at least one document, i.e employee,

the department is included in the outermost has_child query

causing the company to be included within the results.

Hopefully that makes sense.

Let's try to run the query and check the results.

As we can see, a company is matched,

more specifically, the company that I added a moment ago.

The first company is not matched

because it does not contain any departments

where an employee named John Doe works.

Let's try to change the name to my name

and see what happens.

Now, we can see that the other company is matched instead,

and if I change the name to some bogus,

we should see that no company is matched.

So let's do that.

And indeed no company is matched now.

All right, that's it.

That's how to both define and query a multi-level relations.

You just saw an example

of how to query a multi-level relationship.

But you can use the queries

that you've already seen how you wish,

depending on which kind of data you're working with

and which kind of data you want to retrieve.