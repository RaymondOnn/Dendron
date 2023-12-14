---
id: 36oqhe241jag8r5vwzoiaai
title: 127_FuzzyQuery
desc: ''
updated: 1701195293992
created: 1701195267880
---
Instructor: Apart from adding the fuzziness parameter

to the match query

there's also a query that's dedicated to fuzzy queries.

Logically, this query is named fuzzy.

Let's see what that query looks like in action.

So like I said, the query is named fuzzy

and let's search the name field

and search for value of LOBSTER in all capital letters.

And I'll add a fuzziness parameter of auto

and note that this parameter is optional.

It defaults to auto,

but I just wanted to add it explicitly.

Also, I specified the value

to search for in all uppercase letters,

that's just assimilates a user accidentally hitting

the Caps Lock button or something like that.

Okay, running the query,

we don't get any matches.

Why is that?

The reason is that there is a significant difference

between the fuzzy query and the match query

with a fuzziness parameter.

The match query is a full-text query

and the fuzzy query is a term-level query.

As you know, this means that

the fuzzy query is not analyzed.

Since we're searching a field

that uses an analyzer

with the lowercase token filter,

our query does not match

what stored within the invaded index.

Not only does it not match,

but the edit distance required

to make the terms match is too high.

Other than the fact

that the query is not analyzed,

the fuzziness part of it works exactly the same

as you saw with the match query.

That's why I entered an uppercase term for the query

because this is very important to understand.

It's quite rare to search fields

that are not analyzed with fuzzy queries,

which is the reason why the match query

with the fuzziness parameter

is usually preferred over the fuzzy query.

If I change the query to a lowercase one,

then you'll see that we get the same matches

as with the match query

including the product containing the term oyster.

So let's try just to verify that

what I just said is true.

Let's scroll down to the bottom

and verify that the product

with the term oyster within this name field is matched.

So as you can see,

the fuzzy query produces the same results

as the match query in this example

but that might not always be the case.

If you do make use of the fuzz query,

then you have to keep in mind

that the query is not analyzed.

So it's very easy to run into problems

if you use the query on analyzed fields.

So unless you have a good reason for doing so

you should prefer using the match query

with a fuzziness parameter.

