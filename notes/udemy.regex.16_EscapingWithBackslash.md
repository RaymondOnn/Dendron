---
id: 9nkk58ecrl677maoreca9vy
title: 16_EscapingWithBackslash
desc: ''
updated: 1700050796820
created: 1699969287531
---
Lets look at example #11 now.

Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

If you observe all the green ones, you'll notice that they all contain recurrences of the letter 'x'....

followed by a period..

.followed by recurrences of the letter 'y'

The red ones break this rule.

Some of them have the order of 'x's and 'y's reversed, so they are outliers.

The first green entry has the 3 occurences of the letter 'x' followed by a period followed by 2 occurrences of the letter 'y'.

The second green one has 2 x's followed by a period followed by 4y's

The third green has a a single x followed by a period followed by 2y's

Now that we have made our observations, lets move to the next step.

We have picked the 3 green ones alone, and written them in a separate list here.

We'll use an imaginary dotted line vertical separator to divide the strings into sections.

Here we have two such imaginary separators which divide each string into 3 different sections.

The first section is all x's.

The count of the number of x's keeps varying.

But it only x's all the way.

Similary, the last section is all y's.

Again, the count of the number of y's keeps varying. But it only y's all the way.

The middle section is a period.

Unlike the x's and y's the period only occurs once. There are no recurrences of period.

To represent this pattern, we will learn a new regex symbol , backslash, also called the escape character.

So in regex language,

certain symbols are literals and certain symbols have special meaning.

For e.g. the letters a,b,c or x,y,z are all literals. They don't mean anything special to the regex engine.

But certain symbols like period, star , square brackets , etc mean something special to the regex engine.

So what if these symbols form a part of our input string? Like in this case, period is part of our input string.

Now if the period symbol appears on our regex pattern, the regex engine will interpret it as a wildcard, which is NOT what we want.

We want the period to be considered as a literal just like 'a', 'b', 'c' and do NOT want to it get interpreted as a wildcard.

What we need to do is - we'll need to escape this character using a backslash symbol.

When a regex engine sees a backslash symbol, it considers whatever letter immediately follows the backslash as a literal.

In other words, the backslash symbol is a way of 'escaping' the symbol from being interpreted as a special symbol.

So, backslash(CLICK) is our new regex symbol. The characters that need to be escaped are period, as in the current example,

caret, dollar, asterisk,square brackets, parantheses etc..

We have seen some of these symbols already. Some, we will seeing in subsequent examples.

So the thumb rule is, if your input strings contain special symbols used in a literal way,

always escape them using backslash in your regex pattern.

Lets get to our final step,

we have to come up with a single regex pattern that can represent all the 3 green strings.

So we will write x* to represent multiple recurrences of 'x' followed by a period,

escaped with a backslash, followed by y* to represent multiple recurrences of 'y'

So x*\.y*. Please note that there are no spaces in between.

I have just spaced it out a bit in this presentation so that the 3 sections look separated.

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

lets move on to the next one.

Lets look at example #12 now.

Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

If you observe all the green ones, you'll notice that they all contain a single 'x' followed by a symbol followed by a single 'y'.

In the green ones, the symbols used are pound, colon and period.

The first green entry has a single 'x' followed by a pound followed by a single 'y'.

The second green one has a single 'x' followed by a colon followed by a single 'y'.

The third and final green one has a single 'x' followed by a period followed by a single 'y'.

the red ones follow a similar pattern

but the symbol used in the middle is different

they use an ampersand and a percentage symbol.

Now that we have made our observations, lets move to the next step.

We have picked the 3 green ones alone, and writtem them in a separate list here.

We'll use an imaginary dotted line vertical separator to divide the strings into sections.

Here we have two such imaginary separators which divide each string into 3 different sections.

The first section is the single 'x'. It is fixed.

The last section is the single 'y'. It is again fixed. The middle section has different characters

pound in the first one, colon in the second, and period in the third one.

we could use a character class to represent these set of characters

Now, do these characters have any special meaning to a regex engine?

pound is not a regex symbol, colon is also NOT a regex symbol.

We know that period is a regex symbol. It stands for wildcard unless it is escaped with a backslash.

But surprise, we do NOT need to escape the period in this particular case.

why?

let's see

If a period is inside square brackets, it need NOT be escaped.

In the previous example we did escape period, but then it was not inside square brackets. In this example, the . is inside square brackets.

The period will be interpreted as a wildcard only outside of square brackets.

Inside square brackets, it is simply treated a a literal.

Now, there are some symbols which have special meaning INSIDE square brackets, for e.g.

we saw HYPHEN used for character class ranges inside square brackets.....

...and caret symbol used for negation.

These symbols have special meaning INSIDE square brackets,

so they need to be escaped

But a 'period' does not have any special meaning inside square brackets,

it is simply treated as a literal.

let's move to the last step now

We'll come up with the regex patern. We will write x , followed by a character class, which has

3 characters, a pound, a colon, and a period, followed by a y.

So no need to escape anything with a backslash.

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

Only the green ones should have got matched.

let's move on to the next one

Lets look at example #13 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

If you observe all the green ones, you'll notice that they all contain a single 'x' followed by a symbol followed by a single 'y'.

In the green ones, the symbols used are pound, colon and caret.

The first green entry has a single 'x' followed by a pound followed by a single 'y'.

The second green one has a single 'x' followed by a colon followed by a single 'y'.

The third and final green one has a single 'x' followed by a caret followed by a single 'y'.

the red ones follow a similar pattern

but the symbol used in the middle is different,

they use an ampersand and a percentage symbol.

Now that we have made our observations, lets move to the next step.

We have picked the 3 green ones alone, and writtem them in a separate list here.

We'll use an imaginary dotted line vertical separator to divide the strings into sections.

Here we have two such imaginary separators which divide each string into 3 different sections.

The first section is the single 'x'. It is fixed.

The last section is the single 'y'. It is again fixed.

The middle section has different characters,

pound in the first one, colon in the second, and caret in the third one.

We could use a character class to represent these set of characters.

Now, do these characters have any special meaning to a regex engine?

pound is not a regex symbol, colon is also NOT a regex symbol.

But caret is a special symbol. Does it have a special meaning inside a character class?

i.e. inside square brackets? Yes it does. In example#7, we saw how caret can be used inside square brackets

for negation.

The regex engine will therefore interpret caret as the negation symbol if we do NOT escape it.

So we can escape it with a backslash. Lets see..

So if any of the characters caret or hyphen appear inside square brackets, it needs to be escaped with a

backslash as these two characters have special meaning inside square brackets.

let's move to the last step now

We'll come up with the regex patern.

We will write x , followed by a character class, which has 3 characters, a pound, a colon,

and a caret preceded by a backslash, then finally a 'y'

I have highlighted both the backslash and caret together in a single color so that you read it together as one entity.

This is just for clarifying the concept.

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

with what you saw in the graphic for this example.

Only the green ones should have got matched.

lets move on to the next one.

Lets look at example #14 now.

here's our input file

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

If you observe all the green ones, you'll notice that they all contain a single 'x' followed by a symbol followed by a single 'y'.

In the green ones, the symbols used are pound, backslash and caret.

The first green entry has a single 'x' followed by a pound followed by a single 'y'.

The second green one has a single 'x' followed by a backslash followed by a single 'y'.

The third and final green one has a single 'x' followed by a caret followed by a single 'y'.

the red ones follow a similar pattern

but the symbol used in the middle is different,

they use an ampersand and a percentage symbol.

Now that we have made our observations,

let's move to the next step

We have picked the 3 green ones alone, and writtem them in a separate list here.

We'll use an imaginary dotted line vertical separator

to divide the strings into sections

Here we have two such imaginary separators

which divide each string into three different sections

The first section is the single 'x'. It is fixed.

The last section is the single 'y'. It is again fixed. The middle section has different characters,

pound in the first one, backslash in the second, and caret in the third one.

we could use a character class to represent the set of characters

Now, do these characters have any special meaning to a regex engine? pound is not a regex symbol.

But backslash is a special symbol. It is interpreted as an escape symbol by the regex engine and it escapes whatever follows the \.

It is interpreted this way, whereever it occurs, outside character classes or inside,

it doesn't matter

We don't want the regex engine to do this kind of interpretation for the backslash.

We want it to be treated as a literal.

So we'll escape it.

But escape it with what?

Another backslash!

Caret is also a special symbol which has special meaning inside a character class.

So caret also needs to be escaped with a backslash.

So the new rule that we learnt in this example is....

a literal backslash

should always be escaped with a backslash,

irrespective of its position within the regex.

let's move to the last step now

We'll come up with the regex patern.

We will write x , followed by a character class, which has 3 characters, a pound, a backslash escaped by a backslash,

then a caret preceded by a backslash, then finally a 'y'

I have highlighted both the backslashes together in orange so that you read it together as one entity.

I have also highlighted the backslash and the caret symbol together in blue so that you read it together as one entity.

This is just for elucidating the concept.

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

only the green ones should have got matched

So that's it for this session, lets move on to the next one.