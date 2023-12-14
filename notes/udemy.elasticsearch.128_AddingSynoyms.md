---
id: gswakexr9v4ciyrc08ers7y
title: 128_AddingSynoyms
desc: ''
updated: 1701195309817
created: 1701193303971
---
Instructor: We talked a bit about synonyms earlier

in the course,

more specifically in the section about mapping.

What we didn't do though was to actually make use of them.

So that's what we'll be doing in this lecture.

I've added a query

in advance which creates an index named synonyms

with a mapping for a description field.

This mapping makes use of a custom analyzer, which

in turn, makes use of a custom token filter apart

from the standout token and the lowercase token filter.

This is all something you've seen before,

so the interesting part is the custom token filter

because that's where we'll be defining the synonyms.

So the type of the token filter is synonym

and I have already added a synonyms parameter

where we'll be adding an array of synonyms.

Here we need to use a special syntax within strings,

so we're going to be working with an array of strings here.

The way we add synonyms is to specify terms

that should be replaced with other terms.

Let's begin by defining

that the term awful should be replaced by the term terrible

since these terms have the same semantic meaning.

lemme just answer the syntax within a string

in the synonyms array and then explain it afterwards.

Okay, so we have the term to be replaced on the left

followed by an arrow being an equals

and a greater than sign.

The replacement is then added

on the right hand side of the arrow.

So this line essentially means replace any occurrence

of the term awful with the term terrible.

Before proceeding

I just want to mention how synonyms work in Elasticsearch.

Maybe you wondered why I said

that a given term will be replaced by another one.

So if we replace the term awful with terrible,

then what happens if we search for awful?

A document containing either

of the two terms will match

because remember that queries go

through the exact same analysis process.

So if we were to run a match query

against the description field,

the query would also be analyzed with our custom analyzer.

Because of this, documents will still match

even though we are actually replacing terms

within the inverted index.

I'll get back to how synonyms are stored in a moment.

Something important to remember is

that term level queries are not analyzed,

so don't try to combine term level queries and synonyms.

In theory you can,

but it's probably not going to work out so well.

If we were to search for the term awful

with a term level query, it wouldn't match anything

because the term awful is not stocked

within the inverted index

because it has been replaced with the terrible term.

Okay, so far so good.

We just defined our first synonym.

Let's add another one, but this time

with a different syntax.

Suppose that we want to replace the term awesome

with not one, but two terms.

We can do that by separating multiple terms

on the right hand side of the arrow.

So let me just add another string and insert the syntax.

So awesome being the term to replace an arrow

then great comma super.

So we're just separating the terms with the comma.

So what this means is that awesome will be replaced

with both great and super within the inverted index.

When we talked about analyzers,

I mentioned that the positions

of terms is stored within the inverted index.

You already saw how this is used for proximity searches,

but it's also used with synonyms.

What happens when we define multiple replacements

for a term is that the terms are stored

at the same position within the inverted index.

Suppose that we have a document

where the term awesome appears at position five.

When the field is analyzed,

this term gets replaced by both the terms great and super.

These terms appear at the same position

so we effectively have two terms at position five.

This means that each position may contain one or more terms.

The reason for this behavior in regards

to synonyms is that we still want to be able to

perform proximity searches even when making use of synonyms.

Remember that our proximity search is a match phrase query

with a slot parameter.

If elastic search just inserted synonyms

at new positions within the inverted index,

it would effectively be messing up phrase queries.

We can also do kind of the opposite

of the syntax you just saw.

That is to come as separate the number

of terms to the left of the arrow instead of the right.

This replaces any of the terms

to the left with the term specified to the right.

So let's do that.

So what I want to do is to replace elasticsearch

or logstash or kibana with the term elk.

Notice how I entered everything in all lowercase letters.

There's a reason for that

which I'll get back to in just a second.

Yet another syntax is just to specify a comma separated list

of terms.

Let's see an example

of that with the weird and strange terms.

So I'll just enter weird comma strange.

What this does is that both of the two terms will be placed

at the same position, so no replacement happens

with this approach.

Before proceeding,

I just want to mention that the order

in which you define token filters is important

because they're run in the order they are specified.

The reason I mention this is

that it has an impact on how you should define the synonyms.

Notice how I added the lowercase filter

before the synonyms filter.

This means

that the tokens our custom token filter receives are

in all lowercase.

Because of this, we need to define the terms to be replaced

in lowercase as well

because otherwise they wouldn't be matched

because the matching is case sensitive.

Had I switched around the two token filters,

synonyms would no longer work with capitalized inputs.

In that case, the token filter would

potentially receive capitalized tokens and compare them

with lowercase tokens, which wouldn't match.

Also, notice that I entered the replacement values

in lowercase as well, particularly the term elk.

That's because I want all terms

within the inverted index to be lowercased.

Alternatively,

I could just have added the lowercase filter again

after the synonym filter if I wanted to.

The point is that you should keep

in mind where you place the synonym filter.

The letter casing is just a simple example

of how this could go wrong,

but another example could be stemming.

You would want to place a synonym filter

before stemming and generally as early as possible

in the array with the exception of the lowercase filter.

Otherwise, you'll need to account for all

of the token filters when defining synonyms

and that quickly gets hard to maintain when adding

or removing token filters.

With that out of the way, let's run the query

and then experiment a bit with the synonyms afterwards.

To test everything, I'll be using the analyze API

that you saw earlier in the course.

First, I want to check what happens

when analyzing the term awesome.

So what I'll do is that I'll send a post request

to the synonyms index and the underscore analyze API

and use our custom analyzer

which was named my underscore analyzer

matching what we entered above

and specify a text being the term awesome.

Running the query, we can see

that the term is replaced by both greats and super

and that both of these terms are placed at position zero.

Let's take another of our synonym rules

and analyze the term elasticsearch.

So I'll make a copy

of this just to keep our existing queries

and I'll change the text to elasticsearch.

The results show that the term is replaced

with the term elk.

Notice how the term is replaced

even though I capitalized the term.

That's because the lowercase filter is run

before the synonym filter, which I mentioned a moment ago.

Next up, let's try the term weird.

So I'll just make some spaces here,

then paste in a query and write weird as the text.

Here we can see that both the term weird

and the term strange are inserted at the same position.

No term is replaced according to the rule that we defined.

Let's now try to analyze a sentence

and see what the results look like.

So again, I'll be pasting in a query.

Then let's enter a sentence.

I'll say, "Elasticsearch is awesome,

but can also seem weird sometimes."

Looking at the results,

the term elasticsearch has been replaced by elk.

At position two, we have both the terms great and super

which replaced the term awesome.

Scrolling down to the bottom,

we can see that the terms weird and strange are placed

at position seven.

You should now have a good understanding

of how synonyms are stored within the inverted index.

So let's add a document with the sentence

that we just analyzed.

So I'll just copy the sentence, then write a post query

to the synonyms index and the default type.

I will set the description to the sentence I just copied

like so.

Let's add it and now we can run a few search queries

against the index, beginning with a match query looking

for the term great.

All right.

So get synonyms default type, search API.

And the match query

for the description fields searching for great.

This query does match the document even

though the JSON that we sent

to elasticsearch didn't contain the term great.

This is of course

because the term awesome was replaced

with the terms great and super

and since query search the inverted index, we get a match.

Okay, so I just mentioned

that the term awesome has been replaced.

So what happens if we search for awesome

which still want the document to match?

Let's take a look and see what happens.

So I'll make a copy of this query,

paste it in down here, and search for awesome.

This query still matches the document.

That's great, but why is that?

That's because when we send the query to elasticsearch,

it checks which field we are querying, description,

in this case.

It then uses the analyzer that's defined

for that field or the standard analyzer

if none is defined and uses it to analyze the search query.

At least that's the case

for full take searches, as you know.

So the search query goes

through the exact same analysis process as the document did.

So elasticsearch does not look

up the term awesome within the inverted index.

Instead, it uses the results of the analysis process

which involved replacing terms with synonyms.

This means

that we are essentially searching the inverted index

for the replacement values being both great and super.

This just happens for us behind the scenes,

but it means that both the term to replace

and the replacement values can be matched.

This is clever because it means

that elasticsearch does not need to store all

of the terms within the innovative index

and thereby actually save some space.

And that's how you can use synonyms

to improve your search queries.

This is a very powerful feature and it's very easy to use.

An example

of when you would want to use synonyms could be to define

that the words flat and apartment are the same thing.

