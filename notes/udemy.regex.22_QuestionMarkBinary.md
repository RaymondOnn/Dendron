---
id: plr1i1sonpf320vx2e6b4tm
title: 22_QuestionMarkBinary
desc: ''
updated: 1700051034428
created: 1699969463003
---
Lets look at example #23 now.

Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

These kind of strings should be familiar to you.

They follow the pattern of hyperlinks to http websites.

Lets look out for patterns which make the green ones differ from the red ones.

The first green one begins with an https.

The second green one begins with an http.

The red one has an http followed by two s's.

Another red one has httpx instead of https.

Another red one has httpxx.

So the pattern has definitely something to do with the 's' coming immediately after http.

Lets move to the next step.

Lets write the 2 green strings in a list.

We have a pair of imaginary dotted line vertical separators

which divide each string into 3 different sections.

The first section is http

which is fixed.

The last section is

://website which is also fixed.

The middle section is the variable part.

The first green entry has one 's'.

The second green one has zero 's's.

So the number of s has to be either a zero or a one. It is a binary.

It cannot be two s's.

Remember the entry with 2 s's in our input file is marked red. So it should NOT qualify.

Only zero occurences and a single occurences of 's' should qualify.

This brings us to a new regex symbol, the question mark.

it represents only two possibilities.

Either zero repetitions or a single repetition.

So if we write 'a' followed by question mark, it can represent either zero a's which means no a's at all, or it can represent a single 'a'.

Lets make use of this new regex symbol and use it in our final step.

So we will write http as is, because its fixed, then we write 's' - question mark, then we end with ://website which is again fixed.

So our final regex pattern for example#23 is ready. Lets try it out using a regex engine.

Lets bring up our Linux terminal.

First, lets see our input file.

so cat

input file name

there it is...

now grep

hyphen upper case E

the regex pattern in single quotes...

and then the input file name

there you go

Compare this list with what you saw in the graphic for this example.

Only the green ones should have got matched.

So that's it for this session,

lets move on to the next one.