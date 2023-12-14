---
id: l8u0yd4sxsxbtkuh1csgjwf
title: 25_MonitorResolutionProblem
desc: ''
updated: 1700055391728
created: 1699969564935
---
This is a first example for find and replace

example number 25

Here is our input file.

To the right of each input string,

I have given an arrow symbol

and shown what the string should look like

after the replacement is done.

The input strings should look familiar. These are pixel resolutions for monitors.

The strings follow the format: a number

followed by the character 'x' followed by another number.

The first string is 1280x720.

We want to capture the first number and second number from this string... ..and then rephrase the string in another way.

So for 1280x720, the new string should be

1280 pix by 720 pix.

So we need to capture the 2 numbers from the input string and re-arrange it as shown, along with some extra text.

Lets move to step 2 now.

Here I have tried to represent

the input string using regex patterns.

Lets start reading from left to right.

I have represented the first number using [0-9]+.

The square brackets indicate a character class for numbers.

And the plus means one or more occurrences.

So [0-9]+ means a number with one or more digits.

I have enclosed this [0-9]+ within a set of parentheses.

Earlier, we learned that parentheses...

..can be used to segregate groups of characters or symbols together as a single entity.

in the context of the current example

the parentheses also serve as a capture group.

i.e. it helps to capture the pattern represented

by [0-9]+ and assigns a particular number to it. Number 1.

This is done so that this entity can be referred to later, by this number - 1.

If we keep moving from left to right again, next is the character 'x'.

we don't need to capture this

because our final transformed string does not use this 'x'.

It only re-uses the numbers from the original strings.

For this reason, we do NOT enclose 'x' within parentheses. We just write it as such.

let's keep moving again from left to right

The representation for the second number is done in a similar way using [0-9]+.

Again, we enclose this [0-9]+ using another pair of parentheses.

Let me use some colored boxes to highlight the captured groups in parentheses.

So the orange box represents the first capture. Its our capture #1.

The exact identification is 'backslash 1',

you can see that written in a small white font towards the top of the orange box.

We prefix a backslash escape character, so that the '1' is not treated as a literal in our substitution string,

which we'll be seeing shortly

The green box represents the second capture. Capture group #2.

We identify it with backslash 2.

Now we have captured two patterns in all.

Group number backslash 1

and group number backslash 2.

What do we do with this?

that's the final step

here we come up with areplacement string

The string should be of the format 1280 pix by 720 pix. So we write down this phrase,

and in place of the numbers, we use the capture groups.

So whenever the replacement engine sees \1,

it will go back and see what the capture group#1 was.

It will take the contents of capture group#1 and put it in the position of \1.

Similarly, the backslash 2 is replaced with the contents of the second capture group, which is the second number.

So the solution to this problem is actually two regex patterns. The first pattern is what we came up with in Step 2.

The second pattern is our substitution pattern which we came up with, in Step 3.

Lets see all of this in action now.

Lets bring up our Linux terminal.

Up until now, we were using the grep command in Linux to try out all our regular expression examples.

But from this example onwards, we need to do a find and replace.

The grep command will not suffice.

We'll look at another Linux command 'sed'.

sed command can search for a pattern, AND replace it with a substitution string,

all in a single line command invocation.

Yes, it just takes one line in Linux versus a whole class file in Java.

That's why you gotta love Linux.

So the format of the sed command is:

sed hyphen r

letter s forward slash search regex pattern forward slash replacement pattern followed by another forward slash and the letter g.

All of this is enclosed in single quotes. And finally the input file name

let's break it apart and see

The hyphen r enables the POSIX extended set.

IF you recall, we used the hyphen E option in grep to do the same.

the letter s indicates substitution.

Then a forward slash separator.

Then the search pattern regular expression. Then another forward slash separator.

THen the replacement pattern regular expression.

if you recall our solution to the exercise,

we ended up with two regular expressions in the final step, the search pattern, and the replacement pattern.

Then another forward slash separator. ...and finally the g. g stands for global.

By default, sed command substitutes only the first match.

If we add the g option , it will substitute ALL occurrences, which is what we want.

And finally the input file name.

Lets try it out now.

First, lets see our input file.

so cat input file name

there you go...

now sed hyphen r

....and in single quotes... s forward slash...

we'll type in our search regex pattern for this example....

another forward slash

...we'll type in our replacement regex pattern for this example....

another forward slash ...

...g ....

and then the input file name for this example

there you go

So compare the result here with what you saw in the graphic for this example.

Both should match.

Lets now try out the same find and replace example on the Java Regex Engine.

This is the Eclipse IDE.

You can use your preferred Java IDE instead if you want to.

In our Linux session, we gave three inputs to the sed command...

one was the regex pattern, another was the replacement pattern, and finally the input file name.

So we need to do the same in Java as well.

The Java class file that you are seeing right now is RegexFindAndReplace.java

If you look at line numbers 11 and 12, we provide the regex search pattern as well as the input file name.

Make sure you place the input file in the same directory like I have done here.

See that. regex25.txt with the input entries.

Lets go back to our Java class RegexFindAndReplace.java

If you look at line number 28, we give the replacement pattern here.

Now, Java uses the group api on the matcher class to get the substitution string.

So instead of backslash 1,

we have to do a m.group(1) in Java

So we have to do some string manipulation here to frame the replacement string.

This code uses two library classes from the regular expressions library that ships with Java.

The Matcher and the Pattern class

So how this code works is: It reads the input file line by line.

Then for each line, it matches the regex pattern against the current line.

If the pattern matches, then that line is selected for doing find and replace.

So lets run this program.

Right click - Run as -> Java Application

Done. You can see the output here in the console..

Note that the results that we got here in the output, match the replacement strings that we saw in the graphic for this example.

So this is how a regular expression Find and Replace is done in Java.

For all subsequent exercises, it is going to be the same drill.

All you need to do is replace lines 11 and 12 with the pattern and the input file name, and line 28 with the replacement pattern, and run.

No need to change anything else in this code.

This class file as well as all the input files are there in the resources zip file available for download, along with this course.

That's it and I'll see you in the next session.