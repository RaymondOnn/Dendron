---
id: yaywzzrbem0vaaqhjk1qcal
title: 30_AnotherPhoneNumberProblem
desc: ''
updated: 1700055563369
created: 1699969663653
---
Lets see the next example: example#30.

Here is our input file.

To the right of each input string,

I have given an arrow symbol and shown what the string should look like

after the replacement is done.

The input strings are 10 digit phone numbers.

The input strings follow this format:

the first section of 3 digits enclosed in parentheses,

followed by a dot, followed by the next 3 digits....

...followed by another dot ...followed by the next 4 digits.

Note that only the first 3 digits are enclosed in parentheses.

Remember, these parentheses are part of the input string itself. Please do not confuse

it with the parentheses that we will be using for capture groups.

If you look at the replacement string, it shows the same phone number in an almost similar format,

the only difference is that the parentheses enclosing first 3 digits are absent.

So almost all of the information from the input string needs to get captured and get re-used in the transformed string,

except for the parentheses

let's move to step 2 now

Here I have tried to represent the input string using regex patterns.

let's start reading from left to right

Note the first two characters - a backslash and a parentheses.

This parentheses is the one in the input string.

We have to represent it

in our regex pattern.

But parantheses mean something else to the regex engine.

so we need to escape the parentheses

so that the it is seen as a literal, by the regex engine.

If we move further to the right...

I have used a numeral character class,

followed by the curly bracket repeater with 3 repetitions.

This represents the first 3 digits of the phone number.

I have enclosed it in parentheses

and called it capture group: backslash 1.

Note that these parentheses are NOT escaped.

This is because these are NOT literals. These parentheses are special instructions to the

regex engine to capture the contents what is inside.

So we should NOT escape them.

let's keep moving to the right

now we represent the closing parentheses

Again, note that we need to escape the closing parentheses, as it is part of input string, and we want the regex engine to see it as a literal.

Next we represent the dot,

then the second section of the input string which is of 3 digits.

We use a numeral character class with 3 repetitions for this.

Then another dot,

then the last section of 4 digits using another numeral character class with 4 repetitions.

Now we will take this portion of the input string

which starts from the first dot upto the very last digit,

and enclose it in parentheses

We will call it capture group backslash 2.

why do we do it this way?

Take a look at the first input string.

914.582.3013

with 914 in parentheses

Now start reading from the first dot.

so .582.3013.

Now look at the transformed string.

The dot 582 dot 3013 appears as such in the transformed string also.

So we can enclose this in one big capture group.

That is what we have done in capture group backslash 2.

let's now move to the final step

here we come up with the replacement string

the string should be of the format

the first section of 3 digits with NO enclosing parentheses,

followed by a dot, followed by the next 3 digits....

...followed by another dot ...followed by the next 4 digits.

So we write down this phrase,

we write backslash 1 in place of the first 3 digits.

No space or hyphen or anything . We straightaway write \2 which represents the rest of the captured phone number string.

So our solution for example#30 is again a set of two regex patterns.The first pattern is what we came up in Step 2.

The second pattern is our substitution pattern which we came up with, in Step 3.

Lets try this out now.

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