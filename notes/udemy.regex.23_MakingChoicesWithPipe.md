---
id: ijzqxz3m6h5zp6wybzv2776
title: 23_MakingChoicesWithPipe
desc: ''
updated: 1700051063662
created: 1699969495109
---
Lets look at example #24 now. Here is our input file.

The strings with the green check mark on the left, are the strings that I want to select.

The strings with the red cross mark on the left, are the strings that I want to exclude.

The strings are all types of wood like sapwood, rosewood etc.

What is common between ALL the words?

They all end in wood.

Are there any patterns which make the green ones differ from the red ones?

We can't make out anything that is obvious at this point.

so let's move to the next step

let's write the two green strings in a list

logwood and plywood.

We have an imaginary dotted line vertical separator

which divides each string into two different sections

The last section is wood which is common to both strings, so its fixed.The first section is log for the first string and ply for the second string.

Again there is no pattern common to log and ply.

So lets learn a new regex symbol now. The pipe symbol inside parantheses.

Pipe is a way of making an alternative choice between two options.

So we see say a pipe b inside parantheses,

it means either 'a' or 'b'

The parantheses just groups it into a single entity.

Lets make use of this new regex symbol and use it in our final step.

So we will write , inside parantheses,

log pipe ply, then wood.

So the choice can be either log or ply.

If the choice is log, it is read as logwood. If the choice is ply, it is read as plywood.

Other than logwood or plywood, it won't match any other string.

So our final regex pattern for example#24 is ready. Lets try it out using a regex engine.

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