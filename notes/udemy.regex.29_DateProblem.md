---
id: ypxxj62uiggug9b7o3txf2q
title: 29_DateProblem
desc: ''
updated: 1700055520795
created: 1699969642540
---
Lets see the next example: example#29.

Here is our input file.

To the right of each input string,

I have given an arrow symbol and shown what the string should look like after the replacement is done.

The input strings are dates. The input date strings follow a format:

a 3 letter representation of the month

followed by a date number followed by a suffix like th, nd, st etc. followed by a 4 digit year.

If you look at the replacement string, it shows the same date, but in a different format.

it follows the format

date number followed by a hyphen followed by a 3 letter month followed by a hyphen followed by a 2 digit year.

Note that the year is 2 digit and not 4 digits in the transformed string.

So almost all of the information from the input string needs to get captured and get re-used in the transformed string.

Lets move to step 2 now. Here I have tried to represent the input string using regex patterns.

Lets start reading from left to right.

I have represented the first section using a character class of lower case and upper case English letters,

followed by the curly bracket repeater with 3 repetitions.

So this will capture the 3 letter representation of the month,

by enclosing it in parentheses.

Lets call this capture group backslash 1.

Next is the space in the input string.

We don't need to capture it. So we will write backslash s ,

outside of any parantheses, and keep moving.

Next is the section for the date number. I have used a numeral character class,

followed by the curly bracket repeater with 2 repetitions.

Lets capture this by enclosing it in parentheses,

and call this capture group backslash 2.

Next we have the suffix for date like th, nd, st, rd etc.

If you look at the transformed string, we don't use this suffix at all.

So we represent it using a character class

of lowercase characters

followed by the curly bracket repeater with 2 repetitions.

No need to capture it.

So we will NOT enclose it in parentheses.

We will move ahead.

next is another space in the input string

we don't need to capture it

Again we will write backslash s and keep moving.

next we have the four digit year in the input string

We can represent this using a numeral character class with 4 repetitions.

But the problem is that, out of these 4 digits in the input string year, only the last 2 digits are used in the transformed string.

so we will split the representation for this

First, we will write a numeral character class, followed by the curly bracket repeater with 2 repetitions.

Then we will repeat by writing another numeral character class

followed by the curly bracket repeater with 2 repetitions.

We'll take the last character class alone, and capture it by enclosing it in parantheses.

This way, we have captured the last 2 digits of the year alone into capture group backslash 3.

Lets now move to the final step.

Here we come up with a replacement string.

The string should be of the format:

date number followed by a hyphen followed by a 3 letter month followed by a hyphen followed by a 2 digit year.

So we write down this phrase,

we write backslash 2 in place of date number.

We write the hyphen as such.

Then we write backslash 1 in place of the 3 letter month.

We write one more hyphen. And finally, we write backslash 3 in place of the last 2 digits of the year.

So our solution for example#29 is again a set of two regex patterns.The first pattern is what we came up in Step 2.

the second pattern is our substitution pattern which we came up with in step 3

let's try this out now

Lets bring up our Linux terminal.

First, lets see our input file.

so cat input file name

there you go...

now sed hyphen r ..

..and in single quotes...s forward slash...

we'll type in our search regex pattern for this example..

.. another forward slash

...we'll type in our replacement regex pattern for this example....

another forward slash ..

..g ...

.and then the input file name for this example

there you go

So compare the result here with what you saw in the graphic for this example.

Both should match.

So that's it for this session, lets move on to the next one.