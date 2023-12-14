---
id: 5enuq6xbcphsmhcbl1oktf0
title: 15_CharacterClassesWithRanges
desc: ''
updated: 1700050683491
created: 1699969244710
---
Lets look at example #8 now.

here's our input file

the strings with the green check mark on the left

are the strings that I want to select.

the strings with the Red Cross mark on the left

are the strings that I want to exclude.

less lookout for patterns which make the green ones differ from the red ones

Something that is very obvious is that all words end in 'oo'. So they are all oooos.

The green ones are joo, koo , loo and moo.

The red ones also are ooos...They are boo, woo, zoo, coo.

Pattern wise, there is nothing else that makes them differ.

It's just that the green ones uses one set of letters as the staring letter, the red ones use another set of letters.

let's move to the next step

We have a single imaginary dotted line vertical separator

which divides each string into two different sections

On the left of the separator we have a single letter, j for the first string, k for the second, l for the third, and m for the fourth.

On the right of the separator we have 'oo' which is common to all strings.

If you notice these letters, they are all in sequence.i.e., j, k, l, m are

consecutive letters

in the ENglish alphabet written out in sequence.

with this in mind

lets look at a variation of the character class. The character class is represented by square brackets with some content in between.

instead of simply listing out all the letters

we will use a RANGE expression to represent the letters as they happen to be in sequence.

So if you write 'a' hyphen 'c' inside square brackets it represents all letters in the range that starts with 'a' and ends with 'c'.

The letters in this range would be a,b and c.

To fill that single character position,

we have 3 choices, 'a', 'b' or 'c'. The range can be any range in the ASCII character set.

the only condition is that the ASCII value of the range starting character

should be less than the ASCII value of the range's ending character

the ASCII value of lowercase ais 97

and lower case 'c' is 99, so we are satisfying this condition alright.

Lets apply this range character class to our current example.

let's move to our final step

So to the left of the vertical separator,

we have created a character class range which starts with the letter 'j' and ends with the letter 'm' inside square brackets.

It would represent the letters 'j', 'k', 'l' and 'm'

Please note that the letters don't have any spaces or commas in between them

We simply write 'j' followed by a HYPHEN followed by a 'm' with no spaces in between.

Then we have the fixed letters , 'o', 'o'.

So the final regex string would be character class range 'j'-'m', followed by two 'o's.

Now if you notice the strings which have the red cross mark by their side, they begin with the letters 'b','w','z' and 'c'

So by restricting the character class to the letters 'j','k','l' and 'm'

we are , in effect, excluding all the other letters in the red marked strings.

So by bringing in this character range class, we have created a differentiator

between the green and red marked strings.

So our final regex pattern for example#8 is ready. Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so catc

input file name

there it is

now grep

the regex pattern in single quotes.

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

Only the green ones should have got matched.

let's move on to the next one

Lets look at example #9 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

let's look out for patterns which make the green ones differ from the red ones

What strikes you first is that all words end in 'oo'. So they are all oooos.

The green ones are joo, koo , loo , moo and zoo.The red ones also are oos...They are boo, woo and coo.

Pattern wise, there is nothing else that makes them differ.

It's just that the green ones uses one set of letters as the staring letter, the red ones use another set of letters.

let's move to the next step

We have a single imaginary dotted line vertical separator which divides each string into 2 different sections.

On the left of the separator we have a single letter, j for the first string, k for the second, l for

the third, m for the fourth, and 'z' for the fifth.

On the right of the separator we have 'oo' which is common to all strings.

If you notice these letters, four of them are in sequence, j, k , l, m. We also have a letter 'z' which is an outlier.

Lets look at a variation of the character class which uses a combination of a range as well as individual letters.

Instead of simply listing out all the letters, we will use a RANGE expression to represent the letters as they happen to be in sequence.

So if you write 'a' hyphen 'c' inside square brackets

it represents all letters in the range that starts with 'a' and ends with 'c'.

The letters in this range would be a,b and c.

Immediately after 'a' hyphen 'c' we write the letter 'x' inside the square brackets itself.

So the character class now represents a UNION of the range

'a' HYPHHEN 'c' plus the character 'z'.

So if we expand it out, it will represent the letters - 'a','b', 'c' and 'z'

So to fill that single character position, we have 4 choices, 'a', 'b','c' or 'z'.

Lets apply this combination character class to our current example.

let's move to our final step

so to the left of the vertical separator

we have created a character class range

which starts with the letter j and ends with the letter m

inside square brackets

plus the letter z

it would represent the letters jklm and z

Please note that the letters don't have any spaces or commas in between them

We simply write 'j' followed by a HYPHEN followed by a 'm' followed by 'z' with no spaces in between.

Then we have the fixed letters , 'o', 'o'.

So the final regex string would be character class range 'j'-'m', followed by a single letter

'z' inside the square brackets followed by two 'o's.

Now if you notice the strings which have the red cross mark by their side, they begin with the letters 'b','w' and 'c'

so by restricting the character class to the letters

'j','k','l','m' and 'z'

we are , in effect, excluding all the other letters in the red marked strings.

so by bringing in this combo character class we have created a differentiator

between the green and red marked strings

So our final regex pattern for example#9 is ready.

Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep ...

the regex pattern in single quotes.

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

Only the green ones should have got matched.

let's move on to the next one

Lets look at example #10 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to include.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

what strikes you first

is that all words end in 'oo'. So they are all oooos.

The green ones are joo, Koo , Loo , moo and zoo.

notice that the K and L

are in upper case.

The red ones also are ooos..

.They are boo, woo and coo.

Pattern wise, there is nothing else that makes them differ.

It's just that the green ones uses one set of letters as the starting letter,

the red ones use another set of letters.

let's move to the next step

We have a single imaginary dotted line vertical separator

which divides each string into two different sections

On the left of the separator we have a single letter, lower case j for the first string, upper case K for the second, upper case L for the third,

lower case m for the fourth, and lower case 'z' for the fifth.

On the right of the separator we have 'oo'

which is common to all strings

if you notice these letters

four of them , j, k,l,m,

even though they look like they are in sequence,

they are a mix of upper case and lowercase letters

We also have a letter 'z' which is an outlier.

Lets look at a variation of the character class

which uses a combination of two ranges as well as individual letters

Here it is.

Instead of simply listing out all the letters, we will use two RANGEs expression to represent the

letters as they happen to be part of two sequences.

So if you write lower case 'a' hyphen lower case 'c' inside square brackets

it represents all letters in the range

that starts with lower case 'a' and ends with lower case 'c'....

UNION all letters in the range that starts with upper case 'a' and ends with upper case'c'.

The letters in this range would be lower case a,

lower case b ,lower case c,upper case A,upper case B and upper case C.

Immediately after the range lower case 'a' hyphen lower case 'c', we write the range upper case 'a' hyphen upper case 'z',

after that , we write the letter 'x'

inside the square brackets itself

eight-upper case see then after that we write the letter X inside the square brackets self

So to fill that single character position, we have 7 choices, lower case 'a', lower case 'b',

lower case 'c',upper case 'a', upper case 'b', upper case 'c', or 'z'.

Lets apply this combination character class

to our current example.

let's move to our final step

So to the left of the vertical separator,

we have created a character class with two ranges

one which starts with the letter lower case 'j' and ends with the letter lower case 'm'....

another range which starts with the letter upper case 'j' and ends with

the upper case M

...PLUS the letter 'z'

Please note that the letters don't have any spaces or commas in between them.

then we have the fixed letters oo

So the final regex string would be character class range 'j'-'m', followed by a single letter 'z' inside the square brackets

followed by two oo

Now if you notice the strings which have the red cross mark by their side, they begin with the letters 'b','w' and 'c'

so by restricting the character class to the two ranges and the letter 'z'

we are , in effect, excluding all the other letters in the red marked strings.

So by bringing in this combo character class, we have created a differentiator between the green and red marked strings.

So our final regex pattern for example#10 is ready.

Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep ...

the regex pattern in single quotes.

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

Only the green ones should have got matched.

So that's it for this session, lets move on to the next one.