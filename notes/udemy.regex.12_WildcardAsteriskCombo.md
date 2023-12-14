---
id: 2oqrdbbgdqhtypc0t7hixy5
title: 12_WildcardAsteriskCombo
desc: ''
updated: 1700050604943
created: 1699969168642
---
Lets look at example #3 now.

Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

If you observe all the green ones, you'll notice that they all start with the string foo-f.o.o. ..and they all end with the string bar-b.a.r....

But they also have something between the foo and bar.

The first green entry has nothing in between the foo and bar, no letters.

The second green one has 'abc' in between.

The third green has a 'bxc' in between.

The fourth and final green has only a 'z'

in between.

So the only pattern common to all the green ones is that they all start with a foo and end in a bar and have 'something' in between.

This something is unpredictable. The number of letters in between as well as the letters itself, both are unpredictable.

The red ones start with a bar and end in a foo.

So they break the foo-bar pattern anyway.

now that we have made our observations let's move to the next step

We have picked the 4 green ones alone, and written them out in a separate list here.

We have a pair of imaginary dotted line vertical separators which divide each string into 3 different sections.

the first section is fixed

Its foo for all strings.

the last section is also fixed

its bar for all strings

In the middle part, we have the pattern.

We have no letters in the first one,

3 letters in the second,

3 letters in the third, and one letter in the fourth.

So the pattern is any number of any letters.

To represent this pattern, we will use a combination of the 2 regex symbols we have seen

in the previous examples

, a period and a star So in regex language, a dot star represents

zero of more occurence of a wildcard.... i.e....zero or more occurence of any letter.

So it can represent abc or bxc or simply z.

the count as well as the letters do not matter

Lets make use of this new regex symbol and use it in our final step.

In our final step, we have to come up with a single regex pattern that can represent all the 4 green strings.

So we will write foo as is, because its fixed, then we write dot star, then we end with bar which is again fixed.

So foo...dot star....bar....no spaces in between.

So our final regex pattern for example#3 is ready.

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