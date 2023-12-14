---
id: wolnsc2gd76lkvfayd8l5xn
title: 26_FirstNameLastNameProblem
desc: ''
updated: 1700055419318
created: 1699969594499
---
This is example#26,

and our second example for find and replace.

Here is our input file.

To the right of each input string,

I have given an arrow symbol and shown what the string should look like

after the replacement is done.

The input strings are names of persons.

the strings follow the format

the first name followed by a space followed by the last name

The first string is John space Wallace.

We want to capture the first name and last name from this string.....and then restructure the string in another way.

So for John Wallace, the new string should be Wallace

comma John which is of the format last name comma first name.

So we need to capture the first name from the input string and re-arrange it as shown.

let's move to step two now

Here I have tried to represent the input string using regex patterns.

Lets start reading from left to right.

I have represented the first name using a character class for a-z, as well as A-Z , in square brackets followed by +.

The square brackets indicate a character class for english letters.

Why do we need lower case as well as upper case ranges?

Note that the first name as well as last name begin with a capitalized letter, which is why we

need both lower case and upper ranges inside the character class.

And the plus means one or more occurrences.

So athe -z+ stands for a name formed with one or more English letters.

I have enclosed this a-z plus within a set of parentheses.

This pattern captures the first name and assigns a particular number to it - backslash 1.

If we keep moving from left to right again,

next is the space represented by backslash s.

We don't need to capture this because our final transformed string does not use this space.

It only re-uses the first name and last name from the original string.

For this reason, we do NOT enclose backslash s within parantheses. We just write it as such.

let's keep moving again from left to right

The representation for the last name is done in a similar way using the character class and the plus symbol.

Again, we enclose it using another set of parentheses.

The orange box represents the first capture. Its our capture #1.

The exact identification is 'backslash 1',

you can see that written in a small white font towards the top of the orange box.

We prefix a backslash escape character,

so that the '1' is not treated as a literal in our substitution string,

which he will be seeing shortly

The green box represents the second capture.

Capture group #2.

We identify it with backslash 2.

Now we have captured two patterns in all. Group number backslash 1 and group number backslash 2.

lets now move to the final step

here we come up with the replacement string

The string should be of the format lastname comma first name.

So we write down this phrase, and in place of the first name and last name, we use the capture groups.

So when the replacement engine sees \2,

it will go back and see what the capture group#2 was.

It will take the contents of capture group#2, which was the last name, and put it in the position of \2.

Similarly, the backslash 1 is replaced with the contents of the first capture group, which is the first name.

So our solution for example#26

is a set of two regex patterns. The first pattern is what we came up with in Step 2.

The second pattern is our substitution pattern which we came up with, in Step 3.

let's try this out now

Lets bring up our Linux terminal.

first let's see our input file

so cat input file name

there you go...

now sed hyphen r ....

and in single quotes...

s forward slash... we'll type in our search regex pattern for this example

.... another forward slash

...we'll type in our replacement regex pattern for this example....

another forward slash

....g ....

and then the input file name for this example

there you go

So compare the result here with what you saw in the graphic for this example.

Both should match

so that's it for this session, let's move on to the next one