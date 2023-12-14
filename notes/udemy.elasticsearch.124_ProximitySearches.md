---
id: nn5ajy86rdjtvz98jfk2j43
title: 124_ProximitySearches
desc: ''
updated: 1701195195534
created: 1701193232021
---
Instructor: You previously saw how to search

for phrases with the match phrase query.

When searching for phrases,

each of the phrases terms must appear

in exactly that order for document to match.

That's a pretty strict requirement,

so perhaps we want to relax that requirement a bit.

If we have the phrase spicy sauce,

we might also want to match a document containing

the terms spicy tomato sauce, in that order.

So we might want to relax the constraint

and allow for a number of terms

in between the terms and the phrase

that we're searching for.

That's what we'll be looking at in this lecture.

But first, let's back up a bit

and talk about how the order of terms is stored.

As you know, text fields are analyzed

and part of the analysis process

is to tokenize the text input.

When splitting the input into terms,

the positions of the terms are also recorded

and these are stored in the inverted index.

The phrase that's supplied

for the match phrase query is also analyzed.

Apart from just ensuring that a document contains the terms,

the query also ensures that they appear

in the correct order.

For that, it uses the term positions stored

in the inverted index

which were added when analyzing a given document.

So for a document to match the phrase spicy sauce,

both of the terms must appear in the field

that we're searching.

The position of the term sauce must also be one greater

than the position of the spicy term.

If these two conditions are met,

then the document will match the phrase.

We can see this by running a match phrase query

against the new index that I have created in advance.

It contains the documents that you see here.

So if you want to follow along,

these are the queries you will need to run.

As always, you can find them within the GitHub repository.

So let's go ahead and run this match phrase query

that I entered in advance.

There's nothing that you haven't seen here.

So if we run this match phrase query to match documents

with the spicy sauce phrase,

we only get one match,

specifically the document with both of the two terms

and in the correct order.

Suppose that we want to allow a term

in between the spicy and sauce terms,

we can accomplish this by adding a parameter named slop

for the match phrase query.

The value for this slop parameter

should be an integer representing

how far apart terms are allowed to be

while still being considered a match.

How far apart refers to how many times

a term may be moved for a document to match.

Let's see an example to make this more clear.

So I'll just add a slop parameter right here

and set it to one and run the query again.

When we add the slop parameter to the match phrase query,

it's referred to as a proximity search.

How close the terms that we specify in the query

are together with the matching documents

is referred to as proximity.

You could therefore say that we are searching

for the term spicy and sauce within a proximity of one.

Checking the results,

we can see that another document now matches the query,

more specifically the document

with a title of spicy tomato sauce.

This title contains another term

between the terms that we specified in the phrase.

We allowed this by adding the slop parameter

with the value of one.

The document matches because the term sauce

had to be moved once for the title to match.

Let's take a look at what happens with a simple table.

So we have the terms

that the document contains and the positions

in which the terms are stored within the inverted index.

Within the query, the term spicy is at position one

and the term sauce is at position two.

All we need to do is to move the term sauce

to position three for the document to match.

Any term may now be present at position two.

So all it took was a single move.

This satisfies a slop value of one

which allows a single move to be made

to make a document match.

More formally, the number

of moves is referred to as the edit distance.

Let's try to increase the value

of the slop parameter to two.

We now get another match.

An interesting one actually.

Notice how the spicy and sauce terms

are switched around within the title.

So why does that document match?

The reason is that when using the slop parameter

we're allowing a number of moves to be made to terms,

or more formally, the edit distance.

As long as the edit distance does not exceed the value

of the slop parameter,

the terms may be arranged in any order.

What this basically means is that terms can be moved

around a number of times depending on the slop parameter

and as long as the terms appear

within a proximity that does not exceed the slop parameter,

we have a match.

So with the slop parameter

no specific order of the terms is required,

although a greater edit distance is required

for switching terms around.

I hope that made just a bit of sense, and if not,

hopefully it will after going through the example

with a table as we did with the other query.

So we have the term sauce and spicy

at the positions two and three respectively

and the query specifies the terms in the opposite order.

You might think that switching the terms around

would involve a single move, i.e. an edit distance of one.

That's not the case, however,

as an edit distance of two is needed

because we first need to move either the term spicy

or the term source one position.

In this example, I've just moved the term spicy.

When doing this, the two terms share the same position

and we need to move the term spicy once again

so the order of the terms now match the order

within the document.

Because the edit distance being two

is within the boundary specified by the slop parameter,

the document matches.

So switching terms around for documents to match

is perfectly valid.

Had we specified a higher value for the slop parameter,

it would be possible for one

or more terms to be placed in between the two terms.

Anyways, the point is the terms may be moved

around a number of times to match the document

as long as the edit distance does not exceed

what we have specified for the slop parameter.

I hope that makes sense.

So how do you know which value to specify

for the slop parameter?

Well, there is of course no definitive answer

to that question because it really depends.

That's something you will tweak over time,

either by trial and error or by testing it out

on some datasets.

You should know

that things were a bit simple in our examples

because our phrase query only consisted of two terms.

In the real world, this won't always be the case.

So the value that you specify

for the slop parameter is the maximum edit distance

for all of the terms, not just between any two terms.

So the more terms you have,

the higher you should probably set the slop.

The value really depends on the data

and what kind of matching you're going for.

A decent idea would be to set the slop a bit higher

than you otherwise would

because the proximity affects relevance scores.

If including less relevant documents

within the results is acceptable to you,

then this might be a way to go.

These were the basics

of adding flexibility to the match phrase query.

If you want to relax the requirement

that all terms must appear

in a sequence and in a particular order,

then you can do so with the slop parameter.

