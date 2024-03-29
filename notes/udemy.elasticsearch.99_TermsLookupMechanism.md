---
id: v5k7echakjfi5o0qkbqqfl1
title: 99_TermsLookupMechanism
desc: ''
updated: 1701194556515
created: 1701110065048
---
Instructor: You have now seen both the nested query

and how to map and query join fields.

Next up, we'll be looking at a query

that you have actually seen before, namely the terms query.

So what does this have to do with

join fields you might wonder?

The basic usage

of the terms query looks up documents that contain one

or more terms that we specify in the query

but sometimes we might want to look up

so many terms that it's not feasible to include them

in the query definition explicitly.

For example, if you want to include 500 terms

I'm sure you can imagine that the query will end up

being pretty large and clunky, for that reason.

The terms query supports something called a terms lookup

mechanism that's just really fancy Elasticsearch jargon

for fetching the terms from a document.

Essentially what this lets us do is to specify an index type

and identifier of a document

from which we want to retrieve the terms.

We also need to specify the path

of the field in which the terms are stored.

Let's just dive straight in

and I'll do some more talking afterwards.

So I have prepared a bit

of test data in advance and I have already run the queries.

Remember that you can find them

within the GitHub repository in case you're following along.

So the queries create two indices, users and stories.

Each user has a field name following

which contains the IDs of the users he or she's following.

You can think of this as following someone

on Instagram, Snapchat, Twitter, or Facebook.

Now we want to display the stories for a given user

which will be the stories

of the users he or she is following.

To do that, we need to search for stories published

by the user IDs that the user is following.

So I have the first half of a terms query prepared

down here, which searches the stories index.

So now we need to specify which user IDs we want to

find the stories for.

To do this

we need to add an object containing a number of options.

The key should be the name

of the field that contains the user ID of the story

which is user in this case.

So let's add this object user and then empty object for now.

Within this object, we need to define where

Elasticsearch should retrieve the user IDs from.

This should be a document, so we need to specify the details

of which document contains this information.

Here we want to retrieve who we're given users

following being the following fields

within the user's index and the underscore doc type.

So let's go ahead and define that.

So first up, I'll define the index as users

the type as underscore doc

and then let's retrieve the document with an ID

of one and define the path to the field as following.

In this case

the following fields just contains an array of user IDs

but it could also have been mapped as nested objects.

In that case, we could just have defined the path

as following.ID for instance, exactly as you saw

in the lecture covering the nested data type and query.

Alright, let's go ahead and run the query.

Within the results, we can see

that two stories were matched being the stories

from users two and three.

This is what we expected since the user

with an ID of one follows these two users.

So everything works as we intended.

Alright, so what just happened here?

When the coordinating note received the search request

it passed it and based on the options that we specified

it determined that it needed to retrieve the terms to

be able to fulfill the terms query.

As a result, it sent

off a get request to retrieve the document

with the ID that we specified and took the value

of the following fields and fed it to the terms query.

So essentially this is the same as first looking up the user

by ID at the application level and then taking the value

of the following fields and use that for terms query.

Well, it's conceptually the same but not quite the same.

If we were to do this at the application level

we would be hitting the Elasticsearch cluster

with two queries instead of one.

The time it takes Elasticsearch to pass a query

is very limited.

So the difference is mainly related

to the time it takes to send the queries over the network.

There is some network latency

although the Elasticsearch cluster is hopefully

running close to the application server geographically.

More importantly though is how much data will be transferred

over the network because this impacts performance as well.

Suppose that a user is following thousands of other users

that's quite a bit of data to transfer on the network

and it will be even worse if we're not just using integers

but we're using UUIDs or something like that.

Whatever the case.

The point is that it's more efficient to

let Elasticsearch do this internally.

How big the performance win is depends

on how the data is mapped and how much of it there is.

On a quick note, you should know

that the performance of the query degrades gradually based

on how many terms there are.

This is because Elasticsearch needs memory

and processing power for each term.

So if you're dealing with lots of terms

performance may be affected.

To avoid affecting the stability of the cluster.

There is a limit of around 65,000 terms

although this can be configured at a per index level.

Just keep this limit in mind.

If you anticipate

that you'll have queries that need to handle lots of terms.

Alright, so in this lecture you saw

how to use the terms query

to look up terms within another document

potentially within another index

and then use those terms within the same terms query.

And at the end of the lecture we talked a bit

about how this approach improved the performance compared

to doing the same thing with two queries.