---
id: zn2fete16mky3kis2xkwa3a
title: 126_FuzzyMatch
desc: ''
updated: 1701195233878
created: 1701193290294
---
Instructor: Until now,

you've seen quite a few search queries,

but we as developers had full control

of the input for all of them.

Oftentimes, end users will be writing search queries

and will be passing that along to an Elasticsearch cluster.

What if the user makes a typo

or just makes spelling mistakes?

Under normal circumstances that would potentially

cause documents to not match a given query.

Even if documents are actually relevant for the user,

we might want to handle this

so the user still gets relevant results

even if he or she hits the wrong key.

Fortunately, this is easy

with Elasticsearch by using something called fuzziness.

There are a few ways you can do this,

but the most common way is

to add a parameter named fuzziness to the match query.

So that's what we'll start out with before diving deeper

into how fuzziness works.

Let's see an example.

We have a simple match query searching

for lobster within the name fields within the product index.

Running the query,

we get five matches all containing the term lobster,

but suppose though we hit the wrong key

and accidentally hit the zero key instead of O.

So let's try to change the query

and enter a zero like so and run the query again.

Now we no longer get any matches,

which is pretty unfortunate

because obviously the intention was to search

for the term lobster.

Let's fix that by adding a parameter named fuzziness.

For now, I'll just specify a value of auto

and get back to the possible values that you can specify.

So add a parameter named fuzziness like this,

and specify auto as a string.

Okay, and let's run it.

Now we get five matches once again

because we're now allowing a number of characters

to be inserted, deleted,

or substituted to make a query match a given document.

Okay, so now that you have seen an example

of fuzziness in action,

let's take a step back and look at how it actually works.

Fuzziness is implemented

by calculating something called the Levenshtein Distance,

which I'll just refer to as the edit distance.

Suppose that we have two words,

in this case lobster and lobster with a zero.

The edit distance between the two is then the minimum number

of single character edits that are needed to change one

of the words into the other one.

A single character edit can

either be an insertion, deletion, or substitution.

In this example, the edit distance would only be one

because we only have to substitute zero

by the letter O for the words to match.

Before continuing, I just want to mention

that a different algorithm is used for calculating fuzziness

than the match phrase query that you saw earlier.

So you cannot compare the slop and fuzziness parameters

because the edit distances are calculated differently.

With the Levenshtein distance,

we're not moving characters around as we were

with the match phrase query.

I just wanted to mention that in case you're confused

or if you were just wondering.

Okay, so what's the deal with specifying the string auto

for the fuzziness parameter?

As you can probably tell,

we're telling Elasticsearch

to automatically handle fuzziness for us,

it does so by looking at the length of each term

and uses the following rules.

If the length is one or two

then the term must be an exact match,

i.e., fuzziness is not used.

If the length is between three and five,

a maximum edit distance of one is used,

and if the length is more than five,

two edits are allowed.

I should mention that the maximum edit distance

that can be used is two.

This is the case for two reasons.

First of all, studies have shown that 80%

of human misspellings can be corrected

with an edit distance of just one.

So a value of one or two will catch almost all mistakes.

Secondly, being able to specify a higher fuzziness value

would quickly reduce performance.

Also, a higher fuzziness value would mean that you

will begin to see strange and unpredictable results.

For instance,

if we enter a term consisting of five characters

and we're able to specify a maximum edit distance of four,

the results would be all over the place.

So clearly there wouldn't be a good idea.

That's also why leaving the value at auto

is generally the best idea

because otherwise you would quickly find yourself

writing a similar algorithm

determining the appropriate value based on the input length

to avoid strange matches.

If you have control over the query

then you can indeed specify the value yourself,

but situations where that makes sense are pretty rare.

So unless you know what you're doing,

you should leave the value at auto

if you want to enable fuzziness.

Let's make a copy of the query and change the query back

to lobster this time without a zero

Like so.

Let's run the query again.

Perhaps you recall that we got five matches for the query

without fuzziness, and now we can see that we get six.

If we scroll through the results,

we'll eventually find a document

that doesn't contain the term lobster

within this name field.

Instead, it contains the term oyster.

Let's take a look at why this document

is matched by the query.

Since the fuzziness parameter is set to auto

and the length of the query's term is seven,

and edit distance of two is allowed

to make the word lobster match oyster,

we need to first remove the leading L

and then substitute the B with a Y.

Since that's only two edits,

this means that the document actually matches the query

even though we are actually dealing

with an entirely different word.

This doesn't happen very often,

but it's definitely possible.

All right, so I mentioned that the fuzziness

is on a per term basis,

but let me just quickly prove that to you.

I'll just make a copy of the existing query

and set the boolean operator to and,

and the fuzziness parameter to one,

and I'll also change the query.

So let's get going with that.

So copy this one, paste it in, and as the first thing,

I'll specify the boolean operator and set it to and,

then I'll change the fuzziness parameter to one,

and also change the query to lobster with a zero and love.

The idea here is to simulate a user intending to search

for the phrase lobster live.

So let's run the query and notice how it matches a product

with the name Lobster Live.

Since we misspelled both terms,

an edit distance of two is required for the query in total.

But since the fuzziness parameter is applied

to each term individually, a value of one is enough

because only a single edit needs to be made for each term.

If the value was for the query as a whole,

two edits will need to be made, but that's not the case.

So that's why the document matches

with a fuzziness parameter of one and not two.

All right, one last thing.

I mentioned that the Levenshtein distance was the number

of insertions, deletions, and substitutions

of single characters for one string to match another.

There's an addition to this though.

A guy named Frederick Damerau

expanded the Levenshtein Algorithm

with so-called transpositions.

A transposition basically means

to switch two adjacent characters around.

This means that if we have the characters A and B next

to each other, they may be switched around

to B followed by an A.

Transpositions count as a single edit.

So let me show you an example of that.

Let's make a copy of the existing query once again.

Okay, let's clean up the query a bit.

I'll remove the operator parameter,

I'll leave the fuzziness parameter at one,

and let's change the query to lvie.

And this is to simulate a user making a spelling mistake

searching for the term live.

Within the results, notice how the document

with the term live matches the query.

This is because the letters V and I can be switched

around as a transposition, which only counts as one edit.

Since that satisfy the maximum edit distance,

the document matches.

Note that there's no single edit

that can be made to satisfy a maximum edit distance

of one without transpositions.

We can see this if we disable transpositions

by setting the fuzzy_transpositions parameter

to false.

Since we can disable transpositions,

this means that they're enabled by default.

Okay, so let's disable them.

fuzzy_transpositions

with a value of false.

Let's run the query.

And now the document does not match anymore

because two edits would be needed without transpositions

and that is how to add fuzziness to match queries.

