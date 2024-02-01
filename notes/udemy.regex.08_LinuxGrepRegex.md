---
id: cm81cut7v4wbuq50k7uyuwp
title: 08_LinuxGrepRegex
desc: ''
updated: 1704122037901
created: 1700055739430
---
Hello and welcome to the session. All major programming languages like Java, Python, Javascript as well as Linux commands like grep, sed, awk come with a regex engine. 
Although all regex engines share the same fundamental philosophy, if you look at the complete feature set of each regex engine, they may vary. 
Thats why we have standards. 
One such standard is the POSIX standard. 
POSIX stands for Portable Operating System Interface, and is an IEEE standard. 
Almost all major regex engines are POSIX compliant. 
I will be using POSIX regex features only, in this course.This way, whatever regex features that you learn in this course , can be applied to almost all the programming languages as all of them have POSIX compliant regex engines. 
Linux commands like grep, sed , awk etc. 
come with their own regex engine. 
The java programming language comes with its own regex library. 
We will be seeing hands on sessions for Linux grep command, as well as the Java programming language in this course. 
Lets start with the Linux grep command first. 
We'll cover the Java regex engine in the next video. 
I'll bring up a Linux terminal. 
Any standard Linux terminal should do. 
Almost all flavors of Linux come with the grep command pre-installed.
Lets see the contents of the input file for exercise 1 first, the one that we saw in our Deep Dive exercise in the previous video. 
so cat regex01.txt 
Here you go. 
Now lets execute the grep command on this input file. 
We need to provide a regex pattern as well as the input file name to grep. 
So the format goes like this: grep the regex pattern enclosed in single quotes. 
This pattern is what we came up , in our final step , in the Deep Dive exercise. 
foo a* bar Then the file name. 
Here you go. 
So the input file had 6 entries. 
The result has 4 entries. 
The grep command ran the regex pattern against each of the 6 entries in the input file. 
If the entry matcheS the pattern, then it makes it to the output list. 
So we got 4 matches. 
Go back to your deep dive example and see the graphic. 
You can confirm that the results that you see here, are the same as the entries marked in green in the graphic. 
So this is how you try out a regular expression using the grep command. 
I will be providing hands-on sessions for ALL the examples in this course, but I'll do it at a slightly faster pace going forward, as most of it is going to be repetitive. 
With this, we come to the end of this hands-on session with grep. 
Lets move on to the next session.