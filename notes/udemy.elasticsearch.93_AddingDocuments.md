---
id: 4nnb04h79qddmov4c4f9139
title: 93_AddingDocuments
desc: ''
updated: 1701194426494
created: 1701109886237
---
Now that we have the mapping in place

let's add some documents.

You have already seen how to add documents to an index,

but we need to do things a little differently

when working with document relations.

First off, let's add two departments to the index.

For this I have to find two simple queries in advanced,

which is how we would normally go about

adding documents to an index.

I haven't run them yet, because when using join fields

we need to make a small change.

When adding documents for our relation we need to specify

which relation we want the document to be apart of.

Remember that our mapping states

that a department is a parent of an employee.

Since we want to add a department now

we just need to specify the name of this relation,

i.e. the key value of the join field

that we specified in the mapping being departments.

We do this be adding a key

matching the name of the join field

with a value of department.

We named the join fields join_field in our mapping,

but that's just an arbitrary value of your choice

as long as it matches what's defined in the mapping

it can be anything you want.

All right so let's go ahead and add a key named

join_field matching the field name within the mapping,

and set the value to department,

and that's it, let's try to run the query.

Alright, that went well.

So let's go ahead and do the same thing

to the second query as well.

So I can just make a copy of this line

and paste it in down here

and run the query as well.

Note that you can also add an object

as the value for the join field

and define the name of the relation

as the value for a name option.

I just used the shortcut in this example,

so you're welcome to choose whichever syntax you prefer.

Okay, now that we have two departments within the index

it's time to add a couple of employees.

I'll add three fields for each employee,

name, age, and gender, so let's go ahead and type this out.

PUT department _doc

and give it an ID of three.

Set the name field to my name.

Set the age to some number

and the gender to M.

Since we're now dealing with the child side of the relation

we need to specify which document will be the parent

of the document, i.e. which department

the employee belongs to.

As before we do this by adding a key

matching the name of the join field,

but this time we have to add an update as the value.

So let's get started typing that out.

So join_field as before, but this time an object.

Within this update we need to add an option called name

with a value of employee,

which tells Elasticsearch that the document is an employee.

So name and employee.

Last but not least, we need to add an option named parent

with the ID of the parent document as the value.

In this example that should be the ID of a department,

so let's just enter one as the value, so parent and one.

Let's go ahead and run the query.

As you can see we get an error

when trying to add the document.

The error states that routing is missing for the join field,

so what's that all about?

Just to quickly refresh your memory

in case you forgot what routing is,

it's a way for Elasticsearch to know

on which shard child a document with a given ID is stored.

This is both used when indexing new documents

and when finding existing ones.

The default routing behavior

is to use a document's ID as the routing value

and feed that into a hashing function.

The details of how routing works

is not necessary to understand

in regards to joining queries.

So don't worry about that for now.

The point is that we need to add a query parameter

named routing with a value matching the ID

of the parent document, which is one in this case.

The reason we need to do this

is that parent and child documents

must be stored on the same shard

and this is ensured by using the parent's ID

as the routing value.

Remember that since we did not specify a routing value

explicitly when adding the parent documents,

i.e. the departments, the document IDs

were being used implicitly by Elasticsearch

when routing the documents to a shard.

All right, so let's go ahead and add

the routing query parameter.

So question mark, routing,

and the parent ID, which is one.

If we run the query again we should be able to successfully

add the document, so let's try.

And indeed it worked this time

and the department and the employee

are now stored on the same shard.

If for whatever reason you made use of custom routing

remember to adjust the value of the routing

query parameter accordingly.

By the way, in case you were wondering,

I left the employee fields out of the mapping

and just let Elasticsearch handle the mapping

for the fields through dynamic mapping.

And that's it, that's how to add documents

and define that a document is related to a parent document.

Let's finish this lecture off

by adding a couple more employees beginning

with one for the marketing department.

So I'll just make a copy of this query right here,

paste it in and adjust it.

So I'll set the ID to four, first of all,

I'll set the name to, let's say John Doe,

aged 44

and the parent will be the department with an ID of two.

Then I'll change the routing query parameter accordingly.

All right, and let's run it.

Just to have more than two employees within the index

I'll paste in a handful of queries and run them.

So I'll do that down here, paste them in,

and I'll just run them one-by-one

like so.

In case you're following along

you can find these queries within the GitHub repository

so that you don't have to type them out by hand.

Now that we have just a little bit of data to play with

let's move on to seeing how we can query

the document relation that we defined within the mapping.