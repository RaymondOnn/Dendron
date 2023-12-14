---
id: 6819qavmeht5ldbrr0bi9o9
title: 17_Anchors
desc: ''
updated: 1700050844764
created: 1699969308655
---
Lets look at example #15 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left,

are the strings that I want to exclude.

You can see that all the strings are a combination of three words - foo, bar and baz in different orders.

If you observe the 2 green ones, you'll notice that both of them begin with foo.

The red ones do not begin with a foo.

They begin with either a bar or a baz.

equipped with this information

let's move to the next step

We have picked the 2 green ones alone, and writtem them in a separate list here.

We'll use an imaginary dotted line vertical separator

to divide the strings into two sections

The first section is the fixed string 'foo'.

The other section is a variable.

There is no specific pattern.

The order of baz and bar could be both ways.

so we will represent the second section

with a generic pattern which is dot *.

but the problem is that

if you simply have the pattern as foo.*, some of the red ones will also qualify.

For e.g. the first red entry is bar foo baz.

This will also qualify for the foo.* pattern.

Because it has foo and baz.

So foo and something.

It doesn't matter that foo is not at the beginning.

So what we need is a way to narrow down the pattern in such a way that, only the strings that BEGIN with foo qualify.

This introduces us to a new regex symbol

- The caret.

Here it is...

So caret is a placeholder that signifies the beginning of a line.

It does not match any character specifically. It is just an anchor which causes a regex pattern

to be evaluated only by matching it with the beginning of the line....

...and not start matching anywhere in between.

But didn't we learn about caret in previous examples?

Didn't we learn that caret is a negation symbol?

Yes, that is still true.

Caret is used as a negation symbol,

but ONLY if it is within square brackets or within a character class.

So the same caret symbol

has two entirely different interpretations

depending on its position

If inside a character class,

it is interpreted as a negation symbol.

If outside a character class,

it is interpreted as a placeholder for the beginning of a line.

let's move to the last step now

We'll come up with the regex patern.

So we'll start with caret.

If we are using caret as a placeholder, then it will ALWAYS be the first thing in your regex string.

Then we will write foo, which is the fixed part.

Then we write .* , which means we do not care about what comes after foo.

We only care about the fact that the line should BEGIN with a foo.

So we have our final regex pattern now. Lets try it out in a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep ...

the regex pattern in single quotes.

..and then the input file name

there you go

Compare this list

with what you saw in the graphic for this example.

only the green ones should have got matched

let's move on to the next one

Lets look at example #16 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

You can see that all the strings are a combination of three words - foo, bar and baz in different orders.

If you observe the 2 green ones, you'll notice that both of them END with bar.

The red ones do not end with a foo. They end with either a foo or a baz.

After having made this observation, lets move to the next step.

We have picked the 2 green ones alone, and written them in a separate list here.

We'll use an imaginary dotted line vertical separator to divide the strings into 2 sections.

The first section is the variable part. We don't really see any pattern here.

So we will represent the first section with a generic pattern which is dot *.

The other section is a bar , which is fixed.

But the problem is that if you simply have the pattern as .*bar,

some of the red ones will also qualify

for example

the first red entry is foo bar baz. This will also qualify for the .*bar pattern.

Because it has something followed by bar. It doesn't matter that bar is not at the end.

So what we need is a way to narrow down the pattern in such a way that, only the strings that END with bar qualify.

This introduces us to a new regex symbol

the dollar

Here it is...

So just like caret is a placeholder that signifies the beginning of a line, dollar is the other half of it.

Dollar is a placeholder that signifies the end of a line.

It does not match any character specifically. It is just an anchor which causes a regex pattern

to be evaluated only by matching it with the end of the line....

...and not do matching anywhere in between.

let's move to the last step now

We'll come up with the regex patern.

So we'll start with dot star,

then bar which is the fixed part, then the dollar symbol.

If we are using dollar as a placeholder, then it will ALWAYS be the last thing in your regex string.

So we have our final regex pattern now.

Lets try it out in a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

the regex pattern in single quotes.

..and then the input file name

there you go

Compare this list

with what you saw in the graphic for this example.

only the green ones should have got matched

let's move on to the next one

Lets look at example #17 now. Here is our input file.

The string with the green check mark on the left,

is the only string that I want to select

The strings with the red cross mark on the left,

are the strings that I want to exclude.

You can see that all the strings contain the word foo somewhere in the string.

If you observe the only green one, you'll notice that it contains only foo and nothing else.

The red ones also have a foo, but foo is not the only thing.

Some red ones have something after the foo, some red ones have something before the foo.

after having made this observation

let's move to the next step

We have picked the green ones, and written it out in a separate list here.

We'll use two imaginary dotted line vertical separators to divide the strings into 3 sections.

In fact, the string itself takes up whole of the middle section.

The pattern that we need is that there should be NOTHING before foo... ...and there should be NOTHING

after foo.

So we will combine the two placeholders that we saw in the previous examples, the caret and the dollar symbols.

so here it is

^ is a placeholder that signifies beginning of a line; $ is a placeholder that signifies end of a line.

let's move to the last step now

We'll come up with the regex patern.

So we'll start with caret,

then foo which is the fixed part,

then the dollar symbol

What this means is that the line should start with foo

and end with foo. Or, foo should be the only thing on the entire line.

Remember the caret placeholder will ALWAYS be the first thing in your regex string.

..and the dollar placeholder will ALWAYS be the last thing in your regex string.

So we have our final regex pattern now. Lets try it out in a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

the regex pattern in single quotes.

and then the input file name

there you go

Compare this list

with what you saw in the graphic for this example

Only the green ones should have got matched

So that's it for this session, let's move on to the next one