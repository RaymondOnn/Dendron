---
id: 64mev8u2xprj7tg7o5ejgid
title: 21_PlusRepeater
desc: ''
updated: 1700051015767
created: 1699969446485
---
Lets look at example #22 now. Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

Lets look out for patterns which make the green ones differ from the red ones.

If you observe all the green ones,

you'll notice that they all start with the string foo ..f..o..o.

..and they all end with the string bar ... b...a...r....

But they also have something between the foo and bar.

They have recurrences of the letter 'a' in between.

let's move to the next step

where we write down the 3 green ones in a list.

The first green entry has 4 a's in between the foo and bar.

The second green one has one 'a' in between. The third green has 2 a's in between.

So the only pattern common to all the green ones is that they all start with a foo and end in a

bar and have repetitions of the letter 'a' in between.

Please note the first red entry...foobar.

..so this one has zero occurences of 'a' in between foo and bar.

But our regex pattern should be devised in such a way that this foobar should NOT be selected

, as it marked in red.

Which means that we cannot use the asterisk regex symbol. If we say a-star, it would mean zero or more occurences of the letter 'a'.

But that won't serve our purpose.

We need to select only one or more recurrences of 'a'. We should NOT select zero recurrences of 'a'.

This brings us to a new regex symbol, the '+'.

The plus symbol is very similar to asterisk symbol.

Whereas the a-star represents zero or more occurences of 'a', a-plus represents one or more occurences of 'a'.

Lets make use of this new regex symbol and use it in our final step.

In our final step, we have to come up with a single regex pattern that can represent all the

4 green strings, but exclude all the red strings.

So our final regex pattern for example#22 is ready. Lets try it out using a regex engine.

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

Lets move on to the next one.