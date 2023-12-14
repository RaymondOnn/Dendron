---
id: tqheo5ujdhfqvrbm0xl9gob
title: 14_CharacterClasses
desc: ''
updated: 1700050667684
created: 1699969222349
---
Lets look at example #5 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

Something that is very obvious is that all words end in 'oo'.

So they are all oooos. foo, coo and loo.

Other than this, what do they have in common,

that makes them differ from the red ones

The red ones also are ooos...They are moo, doo, poo, boo and hoo.

Pattern wise, there is nothing else that makes them differ.

let's move to the next step we will find a way to make them differ

We have a single imaginary dotted line vertical separator which divides each string into 2 different sections.

on the left of the separator we have a single letter

f for the first string, c for the second, l for the third.

On the right of the separator we have 'oo' which is common to all strings.

Lets look at a new regex symbol or rather a new regex class. Its called the character class,

it's called the character class

and it is represented by square brackets

with something in between

So if we write 'abc' inside the character class, what it represents is ONE of the letters 'a', 'b' or 'c'.

please note that it represents only one character position

To fill that single character position, we have 3 choices, 'a', 'b' or 'c'.

Its not a wildcard, you just can't put anything in there. It has to be one of these 3 choices 'a', 'b' or 'c'.

so this is called a character class

Lets apply this character class

to our current example.

The variable characters that are used in our green strings are 'f','c' and 'l'.

let's move to our final step

So to the left of the vertical separator,

we have created a character class with the letters 'f','c' and 'l' inside square brackets.

Please note that the letters don't have any spaces or commas in between them. They are simply written one after the other.

Then we have the fixed letters , 'o', 'o'.

So the final regex string would be character class of fcl, followed by two 'o's.

Now if you notice the strings which have the red cross mark by their side, they begin with the letters 'm','d','p','b' and 'h'

so by restricting the character class

to the letter 'f','c', and 'l' we are , in effect, excluding all the other letters

in the red marked strings

so by bringing in this character class we have created a differentiator

between the green and red marked strings.

So our final regex pattern for example#5 is ready. Lets try it out using a regex engine.

Lets bring up our Linux terminal.

first let's see are input file

so cat

input file name

there it is

now grep

the regex pattern in single quotes.

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

only the green ones should have got matched

so that's it for the session let's move on to the next one

Lets look at example #6 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

You will notice that all the words end in 'oo'. So they are all oooos.

Most of them are the green ones - foo, coo, doo, poo, loo, boo -

there are six green ones

The two red ones also are ooos...They are moo and hoo.

Pattern wise, there is nothing else that makes them differ.

let's move to the next step

We have a single imaginary dotted line vertical separator which divides each string into 2 different sections.

On the left of the separator we have a single letter,

f for the first string, c for the second, then d, p, l and b. On the right of the separator we have 'oo' which is common to all strings.

We are NOT going to introduce a new regex symbol for this example. We are going to use the same character class regex feature

that we used for the previous example ...example#5.

The character class is represented by square brackets with something in between.

So if we write 'abc' inside the character class,

what it represents is ONE of the letters 'a', 'b' or 'c'.

please note that it represents only one character position

To fill that single character position, we have 3 choices, 'a', 'b' or 'c'.

Its not a wildcard, you just can't put anything in there. It has to be one of these 3 choices 'a', 'b' or 'c'.

Lets apply this character class to our current example too.

The variable characters that are used in our green strings are 'f','c', 'd', 'p', 'l' and 'b'.

let's move to our final step

So to the left of the vertical separator, we have created a character class

with the letters 'f','c','d','p','l','b' inside square brackets.

Please note that the letters don't have any spaces or commas in between them.

They are simply written one after the other.

Then we have the fixed letters , 'o', 'o'.

So the final regex string would be the character class of fcdplb, followed by two 'o's.

Now if you notice the strings which have the red cross mark by their side, they begin with the letters 'm' and 'h'

So by restricting to the letter choices in the character class, we are, in effect,

excluding all the other letters in the red marked strings

You would have noticed by now that this example is quite similar to the previous one.

But you would have started seeing a problem now

If there are too many entries inside a character class, it starts to get unmanageable.

In the current example, we had 6.

Is there a better regex pattern that we can come up with, that is not as lengthy?

This is what we'll try out in our next example. We'll try to re-write the regex in a different way in the next example.

So lets implement our current regex pattern for example#6 using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

the regex pattern in single quotes.

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

only the green ones should have got matched

Lets move on to the next one.

Lets look at example #7 now.

here's our input file

The input file is exactly same as the input file we used for e.g.#6.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

You will notice that all the words end in 'oo'. So they are all oooos.We have more of green ones - foo, coo, doo, poo, loo, boo

there are 6 green ones The two red ones also are ooos...They are moo and hoo.

Pattern wise, there is nothing else that makes them differ.

let's move to the next step

If you take a look at the miniature process flow at the top,

and if you read the description for step two

it reads "Identify patterns in the inclusions list or the exclusions list."

For all examples until now, we have been trying to identify patterns in the inclusion list.

Sometimes it might be easier

to represent the exclusion list using a pattern and simply negate it.

So lets take the 2 strings with the red cross and write them in a list.

We have a single imaginary dotted line vertical separator

which divides each string into two different sections

On the left of the separator we have a single letter, m

for the first string and h for the second string.

On the right of the separator we have 'oo' which is common to both strings.

We are NOT going to introduce a new regex symbol for this example.We are going to use the character class regex feature again here,

but with a twist

The first letter inside our character class is going to be a special one

The caret symbol,

sometimes its called the exponent operator. The caret symbol negates the class.

So if we write caret - 'abc' inside the character class,

what it represents is ANY letter OTHER than 'a','b', or 'c'

please note that it represents only one character position

to fill that single character position we can use any letter

but that letter CANNOT be 'a','b', or 'c'.

Lets apply this character class to our current example too.

let's move to our final step

so to the left of the vertical separator

we have created a character class which starts with the negation symbol caret, followed by letters 'm' and 'h' inside square brackets.

Please note that the caret symbol as well as the letters

don't have any spaces or commas in between them. They are simply written one after the other.

Then we have the fixed letters , 'o', 'o' to the right of the imaginary vertical line.

So the final regex string would be the character class of caret-m-h,

followed by two 'o's.

So by using this regex pattern, we have used the exclusion list,

represented it with a pattern, and then negated it.

In the previous example,

we started with the same input file, but we tried to come up with a regex pattern to represent the inclusion list.

but we saw it slowly spinning out of control as we had to accommodate around six letters inside the character class

, it was getting too verbose.

By using this new method, we are making the regular expression more compact and efficient.

this also leads us to another important point

with respect to regular expressions

there can be more than one solution to a problem

the same problem statement could be solved by two or more different regular expressions

So in this example, or any of the subsequent examples,

I might show you one regular expression as the solution

But if you think you can come up with a better one that solves the problem please feel free to go ahead and use it

You can alays use a regex engine to test out your new solution

if you are in doubt

So lets implement our current regex pattern for example#7

using a regex engine.

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

only the green ones should have got matched

let's move on to the next one