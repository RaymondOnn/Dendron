---
id: guwqiiufd0kd7nbax6lzfur
title: 105_SpecifyingResultSize
desc: ''
updated: 1701194795275
created: 1701192852893
---
Instructor: In this quick lecture,

I want to show you how to control the maximum number of hits

that are returned from a search query.

You can do this in one of two ways,

by adding a size query parameter to the request UI

or by adding a size parameter within the request body

when using the Query DSL.

Let's begin with the query parameter.

I have already added a simple query in advance

that matches eight documents.

I have set the _source parameter to false

just so that it's easier for us

to see how many matches are returned.

So to specify how many matches we want a return,

simply add a size query parameter.

So I'll set it to two.

Running the query now,

we can see that only two matches are returned.

We can also see that the total key

still contains the total number of matches,

which is useful for pagination.

It's really that easy to control the size of the results.

Let's do the same thing,

but this time with a parameter within the request body.

For that, I'll make a copy of the current query

and then adjust it.

So I'll paste it in down here

and get rid of this query parameter

because now I want to specify the size

within the request body.

So let's add a size parameter right here

and set it to two this time as well.

So if I run it,

we, of course, still get the same results as before.

Before ending this lecture, I just want to mention

that the default result size is 10,

so you only need to use the size parameter

if you want a different number of hits to be returned.