---
id: pjcrfyrm3smuabblof3jrmh
title: 28_PhoneNumberProblem
desc: ''
updated: 1700055498904
created: 1699969630155
---
Lets see the next example: example#28.

Here is our input file.

To the right of each input string, I have given an arrow symbol and shown what the string should look like after the replacement is done.

The input strings are 10 digit phone numbers.

The strings follow a format: 3 digits, 3 digits and 4 digits, with sections separated by a period or a dot.

The first string is 914 dot 582 dot 3013

if you look at the replacement string

the only digits that have made it are the last 4 digits, i.e. 3013.

all the remaining digits have been replaced

with the letter 'x'.

So for 914 dot 582 dot 3013

, the new string should be xxx dot xxx dot 3013

Lets move to step 2 now.

Here I have tried to represent the input string using regex patterns.

Lets start reading from left to right.

I have represented the first section using a character class 0 to 9 , in square brackets

followed by the curly bracket repeater with 3 repetitions.

The square brackets indicate a character class for numerals.

So together with curly brackets, it means a number of exactly 3 digits.

this should be enough to accommodate the first section

But we are NOT enclosing this within a set of parentheses. Why?

Because the replacement string really does NOT care about these 3 digits.

It replaces all of them with the fixed letter 'x'.

So there is no point in capturing this section with a capture group.

Then we have a period , which is of course, escaped with a backslash.

If we keep moving from left to right again, we have another set of 3 digits represented

using character class 0 to 9 along with curly bracket repeater with 3 repetitions.

this represents the second section of three digits

Again, we do NOT enclose this within parantheses, as the digits from this section will NOT make it to the final replaced string.

so no capture groups so far

let's keep moving again from left to right

Next we have a period , which is again, escaped with a backslash.

Next we have the last section of 4 digits.

This is again represented using character class 0 to 9 along with curly bracket repeater with 4 repetitions.

But this time, we want to capture this section.

Because the 4 digits from this section make it to the final string.

So we should make sure we have a capture group for this, so that we can refer it from the substitution string.

The exact number identification is 'backslash 1', you can see that written in a small white font towards the top of the orange box.

Now we have done all our captures. In fact, we only have one catpure, which is backslash 1.

let's move to the final step

Here we come up with a replacement string.

The string should be of the format: xxx dot xxx dot the last section of 4 digits.

So we write down this phrase, we write the x's as such as they are simply literals. We write the dots also as such.

Note that escaping is required only for search patterns. In the substitution string, we don't need to escape any symbols.

The only thing we escape is the capture group number. For e.g. the first capture group is

represented using backslash 1... ...so that's the only thing that needs to be escaped.

So we write the x's and the dots as such.

Then we write backslash 1.

The backslash 1 will get replaced with the contents of the first capture group,

which is 4 digit number in the last section of the phone number.

So our solution for example#28

is again a set of two regex patterns.

The first pattern is what we came up in Step 2.

The second pattern is our substitution pattern

which we came up with, in Step 3.

Lets try this out now.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat input file name

there you go...

now sed hyphen r ..

..and in single quotes...

s forward slash...

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