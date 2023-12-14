---
id: b1yc5q5276lisyjkokhs66f
title: 131_Stemming
desc: ''
updated: 1701195377795
created: 1701193374908
---
-: In this lecture we're going to talk

a bit about stemming of words.

We already talked a bit about it earlier in the course,

and you even saw an example of how to use stemming

when we talked about mapping.

But I want to recap on that

since stemming is one of the ways in which

you can improve the matches of search queries.

This lecture is therefore more of a recap

than showing you something new,

except that you will see how a highlighter

will highlight stemmed words as well.

Okay, so I've added an index in advance

with a custom analyzer,

which is used for a description field.

I did this in advance because the query contains nothing

that you haven't seen before.

But let's take a quick look, anyways.

The analyzer uses a custom token filter

named stemmer underscore test,

which is of the type stemmer.

The language is set to English

through the name parameter,

but you could name the parameter language as well.

Apart from the stemming, I have also added a couple

of synonyms to a token field

we named synonym underscore test

which the analyzer also makes use of.

Specifically, I've defined a synonym

for the terms firm and love,

which is sufficient for us in this lecture.

If you're following along,

remember that you can find this query

within the get hub repository

so you don't have to type it out yourself.

Anyways, let's also index a document

because let's face it,

an index is not much fun without any documents.

So let's issue a post request to this index,

stemming underscore test,

and the default type, and let's give it an ID of one.

And just add a description.

I'll just enter I love working for my firm,

exclamation point.

As you can see, the description contains two synonyms

and the word, working, which will be stemmed to work.

Okay, let's run it.

Now that we've indexed the document,

let's write a simple match query

that searches for the terms enjoy and work.

You probably see where I'm going with this,

so let's write it out.

I'll just add some spaces

and add the query here.

So, get stemming underscore test,

default, search EPI.

Add a query object with a match query,

and we'll be querying the description field

for the terms enjoy, work.

Running the query, the document matches,

even though the SOS document contains

the terms love and working.

The reason is matches is of course due to synonyms

and stemming respectively.

I already gave you an explanation

of how synonyms work in one of the previous lectures,

so let's focus on stemming for now.

I just wanted to combine the two

just to give you an idea of how powerful

the search engine they enable you to build.

So the query matches with the term work,

but what would happen if we changed it to working?

Well, let's see, and while we're at it,

we might as well change the term enjoy to love as well.

So, I'll change the query to write love working,

and run it again.

The document still matches.

What happens is that the search query is analyzed,

and during the analysis process

the term working is stemmed to work.

This also happens when the document was indexed,

so that's the term that started within the inverted index.

So when Elasticsearch looks up

the inverted index for matches, there is a match.

This means that we can effectively search

for both the term work and working

and we'll still get a match.

This makes the search engine more flexible

because the two words are so closely related.

So we probably don't want to exclude documents

if users use one over the other.

Okay, just one more thing.

When talking about highlighters in the previous lecture,

I mentioned that synonyms are highlighted

even if we're searching for a different word.

For example, we might search for the term enjoy

while the SOS document contained the term love.

In this case, the term love will be highlighted

within the results.

You already know how this works under the hood,

so let me just show you an example of this

by adding highlighting to our query.

I'll also revert the search query to the original one.

So enjoy work, and then let's add

a highlight object and the fields parameter.

The field is description and just leave the value

as an empty object.

Okay, let's run the query.

So, what does this have to do with stemming?

Well, if we look at the results,

we can see that both the terms love

and working are highlighted.

The first is an example of how a synonym is highlighted

even though we didn't actually search for the term love.

In regards to the term working,

this word is still highlighted

even though we searched for the term work.

That's because the word is stored as the term work

within the inverted index.

Despite of this, the original word is still highlighted

because the inverted index also stores the offsets

of the original term,

which are then used by the highlighter.

And that was just a quick recap

of how to apply a stemmer to a field

and how Elasticsearch still highlights the original words

even after stemming has been applied to a text field.

