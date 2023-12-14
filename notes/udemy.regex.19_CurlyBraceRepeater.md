---
id: vhv8x33u434dytj9msn2383
title: 19_CurlyBraceRepeater
desc: ''
updated: 1700050982980
created: 1699969395143
---
Lets look at example #18 now.

Here is our input file.

The strings with the green check mark on the left,

are the strings that I want to select.

The strings with the red cross mark on the left,

are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

If you observe all the green ones, you'll notice that they are all 3 digit numbers.

The red entries are single digit, 2 digit, four digit and five digit numbers,

but none of them are 3 digit numbers.

So we have a clear pattern here.

So lets move to the next step.

We have picked the 3 green ones alone, and written them out in a separate list here.

Now, a digit can be any character from 0 to 9.

So a digit can be represented by a character class

with the ASCII range starting with 0 and ending with 9.

So a 3 digit number can be represented

by 3 such character classes written back to back.

So here we have written all the three strings which are 3 digit numbers

by using the character class repeated 3 times.

We also have a line beginning and line end anchor at the left and right.

Why do we need these? The reason is that we do NOT want matches done with a subset of the

string. i.e. we do not want to match against substrings.

To explain this further, take a red marked number from the input list, say, the fourth red one 45687.

This is a 5 digit number.

Take a substring of this, take the middle 3 characters. 5- 6 - 8 ..

.it forms a 3 digit number.

Just because it contains a 3 digit number

somewhere in between, we don't want to identify it as a positive match. We are only interseted in matching the whole string.

In order to ensure this, we put the anchor

at both ends.

This way, the match will be run only against the whole string and not substrings.

Please note that, we are doing it this way, because

the situation calls for it

In other situations, we might want to search for a pattern anywhere in the string . In that case, anchor should NOT be provided.

Now what if we want to represent 10 digit numbers.

It might be cumbersome to write the character class 10 times over.

We need a better compact way to represent this.

This leads us to our next regex symbol, the repeater.

It is represented by opening and closing curly braces

with a number in between

This number signifies the number of repetitions.

So a followed by the number 'm' in curly braces

means exactly 'm' repetitions of the letter 'a'.

Remember the asterisk that we have been using so far.

The limitation with asterisk is that you can't represent an exact number of repetitions with it.

If you say a*, it could mean zero upto any number of recurrences.

The curly braces repeater allows you to specify exactly 'm' repetitions.

Lets move to our final step, we have to come up with a single regex pattern that can represent

all the three digit numbers

So first we will write the start anchor ,

then the character class for a single digit,

which is 0-9 inside square brackets

Then the number '3' within a set of curly braces

then the end anchor .

So how it reads is: caret -> then a digit character class repeated 3 times -> then a dollar.

So our final regex pattern for example#18 is ready. Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

upper case E

the regex pattern in single quotes.

..and then the input file name

there you go

Compare this list

with what you saw in the graphic for this example.

Only the green ones should have got matched.

let's move on to the next one

Lets look at example #19 now.

here's our input file

The strings with the green check mark on the left,

are the strings that I want to select.

The strings with the red cross mark on the left,

are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

The first green entry is a 4 letter word,

second one tiger is a 5 letter word,

third one mouse is also a 5 letter word

The next one cuckoo is a 6 letter word,

and the last green one deer is a 4 letter word.

The red entries are 3 letter , 7 letter and 8 letter words.

So we don't have a clear pattern here.

so let's move to the next step

We have picked the 5 green ones alone, and written them out in a separate list here.

We have a line beginning and line end anchor at the left and right for the same

reason we had in the previous example - We do not want to match substrings.

We use the character class a hyphen z inside square brackets to represent any English letter.

Then we use the curly bracket repeater symbol with a number inside.

This number signifies the number of letters used in that word.

So if you look at the numbers alone, the lowest one is 4

and the highest one is 6.

So our range of green words are atleast 4 letter words and at the most 6 letter words.

So can we create a range of repetitions rather than an exact repetition?

This leads us to a slightly different flavour of the curly braces repeator. Here it is.

So notice that this one has two numbers inside the curly braces

separated by a comma.

So m and n

denote the range of repetitions

So if we say a, it represents at least 'm' repetitions of the letter 'a' and at most 'n' repetitions of the letter 'a'.

let's move to our final step

we have to come up with a single regex pattern that can represent all the green words.

So we again use the character class [a-z] and follow it with a repeator range from 4

to 6, and enclose it within anchor .

So this means any word made up of English alphabets which is atleast made up of 4 letters and at the most made up of 6 letters.

So this pattern selects 4 letter, 5 letter and 6 letter words only.

And it will exclude all others like 3 letter, 7 letter, 8 letter words etc.

So our final regex pattern for example#19 is ready.

Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

upper case E

the regex pattern in single quotes.

..and then the input file name

there you go

Compare this list

with what you saw in the graphic for this example.

Only the green ones should have got matched.

So that's it for this session, lets move on to the next one.