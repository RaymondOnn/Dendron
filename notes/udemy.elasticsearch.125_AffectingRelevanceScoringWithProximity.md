---
id: 35apyk8x00c54v6iowhkvfr
title: 125_AffectingRelevanceScoringWithProximity
desc: ''
updated: 1701195216485
created: 1701193264454
---
Instructor: Let's take a look at the search results

from the previous lecture

and talk a bit about relevance scoring

in the context of proximity searches.

Notice how the document where the terms spicy

and source appear next to each other

in the correct order is scored the highest.

That's because the proximity

in which the terms appear affects the relevance scores.

The closer the proximity, the higher the relevance scores.

Another way of putting it is to lower the edit distance,

the higher the relevance scores, as you know

how relevant scores are calculated is not so simple.

So there's no guarantee that the documents with the terms

that have the lowest proximity are scored the highest.

That's because a number

of other relevance factors are taken into account

when calculating the relevance scores

and the term proximity is just one of them.

So it does have an effect, but it's by no means

the only factor used when calculating the relevance scores.

So don't expect the scores to necessarily reflect

the term proximity.

Anyways, now that you know

that the proximity affects the relevance scores

I want to show you a useful trick.

It's technically nothing that you haven't seen before

but it's quite useful nevertheless.

So by default

the match phrase query requires all terms to be present

and in the order specified, in the previous lecture,

you saw how to make the arrangement of terms more flexible

and less strict, which we did with the slot parameter.

But maybe we don't want to require all terms to be present

because the relevance scoring algorithm favors documents

where most of the terms appear anyways,

we can do that by using the match query

as you saw earlier in the course.

Nothing new about that.

But at the same time, we might want to boost documents based

on the term proximity, which the match phrase query does.

So how do we get the best of both worlds?

By combining the two queries within a bull query.

Let's begin with a bull query that only contains

a match query.

So I'll make a copy of this

and adjust it just to save some typing

and enter a bull query here and a must array.

And within this array,

I'm going to add a match query clause, first of all,

and I'll search the title fields, change it to an object,

and specify the query as spicy source.

Simple enough.

Let's just run it.

So remember that the default Boolean operator used

for match query is or, so both terms are not required

to be present within a document

for it to be considered a match.

The more terms are present, the higher the relevance score,

though, also remember

that you can specify the minimum number of terms

that should be present

with the minimum should match parameter.

Taking glance the results,

notice how there is some distance between the terms spicy

and source in the top hits.

We can do better than that.

The match query is required to match

but what we can also do is to place

an optional match phrase query within the should objects.

Let's do that.

So apart from the master array, I'll add a should array here

and add a query clause of the type match phrase.

Again, for the title field

I'll change this to an object as well.

Specify a query and I'll specify the same query

for this clause as well.

Alright, so what does this new query mean?

The match query must still match

but this is not the case for the match phrase query.

This query is optional, but if it matches

it boosts the relevance score of matching documents.

We should be able to see this if we run the query.

As you can now see the document

with the two terms right next to each other received

a significant relevance boost.

That's great in this particular example

but we can do better.

We are only boosting the relevance of documents

where the two terms appear right next to each other,

but it would probably be a good idea

to be a bit more flexible on that.

You already know how to do that

by specifying a slot parameter.

Since the match phrase query boost matches based

on how well they match, this means

that we're boosting documents based on the term proximity.

So by specifying a slot parameter, we'll still see documents

with the two terms close to each other at the top.

We're just using the term proximity

as a relevance factor and boosting the documents

where the two terms are close to each other.

Let's add a slot parameter of five like so.

Let's run the query again

and see if there's any difference.

So now we're giving a relevance boost

to documents where the two terms appear in close proximity.

The same documents are matched regardless

of whether or not we add the match phrase query or not.

By adding it, we're just affecting the relevance course

and favoring the documents containing the terms

within the close proximity.

We didn't cover anything new in this lecture

but I just wanted to share this trick

with you as it may be useful to you.

Before ending this lecture, I want to remind you

that there are many factors involved

with calculating relevance scores.

So this approach does not guarantee

that documents with a close proximity are scored highest.

These documents do get a relevance boost

but there's no guarantee

that matches are sorted by the proximity.

That really comes down

to the documents that you have in your index.

So consider this approach a relevance factor

that fits into the bigger picture

and works together with the other relevance factors.

