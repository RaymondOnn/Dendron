---
id: 1m587mtzp68gttxunlev9vp
title: 20_SingleEndedCurlyBracesRepeater
desc: ''
updated: 1700051000699
created: 1699969431561
---
Lets look at example #20 now.

This is an example in the extended regular expressions set

Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

If you observe all the strings

you'll notice that they all have repetitions of the the word 'ha'

Both the red and the green ones follow this pattern.

How can we differentiate the green ones from the red ones?

Lets move to the next step.

Lets write down the green entries in a list.

The first green entry

has 5 repetitions of 'ha'.

So we will write 'ha' and we will use the curly braces repeater with the number as 5.

The second green entry has 4 repetitions of 'ha'. So we will write 'ha' and we will use the curly braces repeater with the number as 4.

And so on and so forth.

The number of repetitions is different. But do they follow some pattern?

The minimum number of repetitions is 4,

let's create a pattern out of this fact

We'll use the new regex sybmol, the parantheses,

which we saw in the previous example

Parantheses are used to treat a group of characters

as a single entity

We will enclose the 'h' and 'a' together in parantheses.

So when the regex engine sees the curly brace repeater, it will take whatever is inside the parantheses and apply the repetition to it.

If we had not used the parantheses, then the curly brace repeater would have

applied to only the 'a' which immediately precedes it.

Also, we have another flavour of the curly braces here.

In a previous example, we used a repetition range inside curly braces,

where we specified the start and end of the range.

Here, we will use a repetition range inside curly braces, where we will specify the start range, but we will NOT specify the end range.

so let's move to our final step

We will write ha in parantheses

followed by repeater curly braces

which have start as 4,

but no end

So this means at least 4 repetitions of 'ha' should be present.

The maximum repetitions coulde be anything.

It could be a hundred repetitions of 'ha'. It would still qualify.

So our final regex pattern for example#20 is ready. Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

hyphen upper case E

the regex pattern in single quotes...

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

Only the green ones should have got matched.

So that's it, lets move on to the next one.

Lets look at example #21 now.

Here is our input file.

The strings with the green check mark on the left,

are the strings that I want to select.

The strings with the red cross mark on the left,

are the strings that I want to exclude.

If you observe all the strings,

you'll notice that they all have repetitions of the word 'ha'

Both the red and the green ones follow this pattern.

How can we differentiate the green ones from the red ones?

let's move to the next step

Here instead of trying to write the green entries in the inclusion list, lets try to write down the red entries in the exclusion list in a column.

The first red entry has 5 repetitions of 'ha'. So we will write 'ha' and we will use the

curly braces repeater with the number as 5.

The second red entry has 4 repetitions of 'ha'. So we will write 'ha' and we will use the curly braces repeater with the number as 6.

and so on and so forth

The number of repetitions is different.

But do they follow some pattern?

The minimum number of repetitions is 3, there is no entry with 1 or 2 repetitions.

So lets create a pattern out of this.

We'll use the new regex sybmol, the parantheses,

which we saw in the previous example.

Parantheses are used to treat a group of characters as a single entity.

We will enclose the 'h' and 'a' together in parantheses.

So when the regex engine sees the curly brace repeater, it will take whatever is inside the parantheses

and apply the repetition to it.

Also, we have another flavour of the curly braces here.

In a previous example, we used a repetition range inside curly braces,

where we specified the start of the range, but not the end.

Here we do it the other way.

We will use a repetition range inside curly braces,

where we will NOT specify the start range, but we will specify the end range alone.

So the red ones have 3 or more repetitions.

So if we negate this statement, the green ones have 2 or less repetitions.

So we can have atmost 2 repetitions.

so let's move to our final step

We will write ha in parantheses

followed by repeater curly braces

which have ending as 2, but no start.

So this means atmost 2 repetitions of 'ha' will be matched. Any repetitions beyond that, we will disqualify that string.

Also we have the line start and line end placeholders, the caret and dollar too.

This is to solve a particular problem.

Consider a string which has , say, 4 ha's. The string can be broken down into 2 equal parts, each part having 2 ha's.

So if you do NOT use the start nend placeholders, the regex will look for anything less than or equal to 2 ha's.

As soon as it sees any section within the string,

which has 2 has, it will match.

So even though the string has 4ha's , the engine will match it.

This is not what we intended.

So to fix this, all we need to do is place a start and end

line placeholder

Once you do this, the engine can only match the pattern with the entire string.

It cannot take bits and pieces from the middle of the string and run its match against that piece.

So our final regex pattern for example#21 is ready. Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

hyphen upper case E

the regex pattern in single quotes...

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

Only the green ones should have got matched.

So that's it for this session,

lets move on to the next one.