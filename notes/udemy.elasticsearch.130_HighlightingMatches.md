---
id: 4b5lisoiws97rrnv7kgzpag
title: 130_HighlightingMatches
desc: ''
updated: 1701195357052
created: 1701193363106
---
Instructor: Did you ever notice how search engines

highlight matching parts of a query within the results?

You probably saw this on Google, Bing,

or whatever search engine you're using.

Elasticsearch can do this too

by using something called a highlighter.

Let's just dive straight into an example.

I've added a test document to a new index in advance,

which you can see at the top.

It just contains a description field,

so there's nothing special about it.

Then I've added a simple match query in advance,

which just searches for Elasticsearch story

within the description field.

It doesn't return the source document

just to make the results easier to read.

What we can do is that we can add a highlight object

at the top level of the request body.

So let's try to do that.

So highlights being an object.

Within this object we must add a fields object

containing the fields

that we want to enable highlighting for.

So fields also being an object.

And since we only have one field,

this is going to be the description field,

so I'll add a key with that name.

So description.

I'll just leave the value as an empty object.

Within this object, we can configure the highlighting,

but I'm not going to do that for now.

That's because highlighting is a huge topic on its own,

and things get pretty complicated.

There are a few different highlighters available,

which do highlighting in different ways.

Unless we specify otherwise,

a highlighter named plain will be used

which is sufficient in most cases.

Anyways, let's run the query and look at the results.

For each match within the hits array,

we now have a new key named highlights.

This is an update containing the fields

that we requested highlighting for in the request.

In this case, only the description field.

The description key then contains an array of the fragments

that match the query.

The reason that we get an array back

is of course the multiple parts

or fragments of the description fields

might match the query.

This is the case in this example

because the term Elasticsearch appears

both near the beginning and the end of the field.

Elastic search returns fragments

because the fields might be very long.

Imagine if the field stored 5,000 words for this document.

Displaying all 5,000 words

and highlighting a word at the beginning and end

would not make any sense.

Instead, notice how Elasticsearch returns fragments

containing words before and after a matching term,

which gives the user a bit of context.

Also, as you probably noticed by now,

the highlights are enclosed within EM tags,

which is the case for both the story

and Elasticsearch terms.

This enables you to easily find the matches

at the application level if you want to do some processing

or just display the fragments directly to the user.

But why do we even need Elasticsearch

to wrap the matches with an EM tags

other than for our convenience?

Couldn't we just do this ourselves

within our application code?

No, not really.

Remember that Elasticsearch analyzes the query,

which potentially involves handling synonyms

and stemming, for instance.

Let's take synonyms for example.

Suppose that we index the document

containing the term happy,

and that we added a synonym of delighted for this term.

Searching for delighted would then match the document

that contains the term happy,

which you saw in an earlier lecture.

But what about the highlights?

Elasticsearch handles this for us automatically

because it highlights the original term at that position

within the source document.

What happens is the following.

Elasticsearch performs a lookup for the term delighted

within the inverted index

and finds a match because the description field

went through the analysis process.

In that process, the term happy

was replaced by the term delighted

within the inverted index,

and that's why there's a match.

Remember that we would still get a match

if we search for happy

because the query is analyzed in the same way.

Remember how the inverted index contains the offsets

for where the original term appeared

within the source document.

So while the term delighted

is stored within the inverted index,

Elasticsearch stores the offsets of the corresponding term

within the source document being the term happy.

This enables the highlighter to wrap the original term

with an attack, even though it's an entirely different term.

So even when searching for the term delighted,

the term happy will still be highlighted within the results.

Pretty cool, right?

The same is the case with stemming and so forth.

The point is that how Elasticsearch wraps matches

within tags is more complicated

than just doing string replacements.

So we really shouldn't try to mimic this behavior

for whatever reason.

Anyways, that was just a bit of background knowledge,

so I hope that made sense to you.

Otherwise, don't worry about it

because you can use highlighters perfectly fine

without understanding what happens under the hood.

There's one more thing that I want to show you

in this lecture, how to wrap matches within a different tag.

That's done with the pre_tags and post_tags parameters.

So let's add these two parameters

within the highlight objects.

First, the pre_tags parameter.

I'll change it to an array, being an array of strings.

So let's say I want to use the strong tag

instead of the EM tag.

And then I can do the same for the post_tags parameter.

So here I just have to close the strong tag like so.

If we run this query again, we can see that the matches

are now wrapped within strong tags instead.

Those were the basics

of highlighting matches in Elasticsearch.

Like I said, there are a number of configuration options

and highlighters available,

but much more than I can cover in this course.

If you need something more specific

than what I covered in this lecture,

then I encourage you to check out the documentation.

For your convenience,

I have attached a link to this lecture.

