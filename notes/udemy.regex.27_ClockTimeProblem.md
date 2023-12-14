---
id: fzqq40meokvvh7xc9kswbp3
title: 27_ClockTimeProblem
desc: ''
updated: 1700055464438
created: 1699969615270
---
This is example#27, and our third example for find and replace.

Here is our input file.

To the right of each input string,

I have given an arrow symbol and shown what the string should look like after the replacement is done.

The input strings represent time on a clock.

the strings follow the format

The hour followed by the minute,

note that there is no AM or PM.

the first string is 7:32

We want to capture the hour and minute from this string.....and then rephrase the string in ...

another way. So for 7:32, the new string should be

32 minutes past 7

let's move to step two now

Here I have tried to represent the input string using regex patterns.

let's start reading from left to right

I have represented the first name using the character class 0 to 9 in square brackets followed by +

The square brackets indicate a character class for numerals. And the plus means one or more occurrences.

So zero to 9 plus means any number with atleast one digit.

This should be enough to accommodate the hour.

I have enclosed this zero to 9 plus within a set of parentheses.

This pattern captures the hour and assigns a particular number to it - backslash 1.

If we keep moving from left to right again, next is the colon represented as such.

We don't need to capture this because our final transformed string

does not use this colon.

It only re-uses the hour and minute from the original string.

For this reason, we do NOT enclose colon within parentheses. We just write it as such.

Lets keep moving again from left to right.

the representation for minutes is done in a similar way using the character class and the plus symbol

Again, we enclose it using another pair of parentheses.

The orange box represents the first capture.

Its our capture #1.

The exact identification is 'backslash 1',

you can see that written in a small white font towards the top of the orange box.

We prefix a backslash escape character,

so that the '1' is not treated as a literal in our substitution string

which we will be seeing shortly.

The green box represents the second capture.

Capture group #2.

We identify it with backslash 2.

now we have captured

two patterns in all.

Group number backslash 1

and group number backslash 2.

let's now move to the final step

here we come up with the replacement string

The string should be of the format: x minutes past y

So we write down this phrase, and in place of the hour and minutes, we use the capture groups.

So when the replacement engine sees \2,

it will go back and see what the capture group#2 was.

It will take the contents of capture group#2, which was minutes,

and put it in the position of \2.

Similarly, the backslash 1 is replaced with the contents of the first capture group,

which is the hour.

So our solution for example#27

is a set of two regex patterns. The first pattern is what we came up in Step 2.The second pattern is our substitution pattern.

which we came up with, in Step 3.

Lets try this out now.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat input file name

there you go...

now sed hyphen r .

..and in single quotes...

s forward slash

. we'll type in our search regex pattern for this example..

.. another forward slash

...we'll type in our replacement regex pattern for this example....

another forward slash ..

..g ....

.and then the input file name for this example

there you go

So compare the result here with what you saw in the graphic for this example.

Both should match.

So that's it for this session, lets move on to the next one.