---
id: 8kmaaiuldypxuxtou2jprhz
title: 11_Wildcard
desc: ''
updated: 1700050568103
created: 1699969139782
---
Lets look at example #2 now.

Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

If you observe all the green ones, you'll notice that they all start with the string foo ..f-o-o and they all end with the string bar ... b-a-r....

But they also have something between the foo and bar.

Its not the same character that comes in between always.

The first green entry has the letter 'a' between the foo and bar.

The second green one has 'x' in between.The third green has a 'c' in between.

So the letter in between foo and bar is unpredictable. It does NOT follow a pattern.

But the number of letters is predictable.

If you observe the green ones, the number of letters between foo and bar is always one. We only have a single letter in between.

The red ones have different number of letters in between.

Lets take the first red one. Ohh, this one starts with bar, so this is outside the pattern anyway.

Lets take the second red one. It has foo and bar.

In between we have nothing,

or to put it in a different way... ....we have zero letters between foo and bar.

Lets take the third and final red one.

It has two letters 'x' and 'y' in between foo and bar.

So we have our pattern now. The green ones have 'exactly' one letter between foo and bar.

Whereas the red ones do NOT follow this one-letter-in-between pattern

Now that we have made our observations,

let's move to the next step

We have picked the 3 green ones alone, and written them out in a separate list here.

We have a pair of imaginary dotted line vertical separators which divide each string into 3 different sections.

The first section is fixed. Its foo for all strings.

The last section is also fixed. Its bar for all strings. In the middle part, we have the pattern.

We have one 'a' in the first one, one 'x' in the second one, and one 'c' in the third.

So the pattern is not the letter itself, but rather the count of the number of letters.

So the count of the number of letters between foo and bar is always 1 for the green ones.

To represent this pattern, lets look at a new regex symbol, which is a dot or a period.

So in regex language, a period is a wildcard that represents exactly one character in a

single position. It will match ANY character in one position.

Lets make use of this new regex symbol and use it in our final step.

In our final step, we have to come up with a single regex pattern that can represent all the 3 green strings.

So we will write foo as is, because its fixed, then we write period, which is the single wildcard character,

then we end with bar which is again fixed.

So foo...period....bar.

Please note that there are no spaces on either side of the period in the real regex string. I have just spaced it out a bit in this

presentation so that the 3 sections look clearly separated.

So our final regex pattern for example#2 is ready.

Lets try it out using a regex engine.

Lets bring up our Linux terminal. First, lets see our input file for this example.

It should be the same input file which you saw in the presentation.

cat inputfilename

Okay, thats our input file. Now we'll use the regex pattern that we came up with in our final step.

Then we'll feed that into the regex engine built into the 'grep' command in Linux, and run it against this input file.

So the format is grep

space the regex pattern enclosed in single quotes,

then the input file name.

So there it is. This is the result. So the regex engine in grep searched the input file using the

regex pattern that we gave it, and it was able to get the following matches.

You can verify that the match results are the green ones marked in our presentation.The red ones have NOT been selected.

So that's it. We have tried out and validated our regex pattern for example 02 using grep's regex engine.