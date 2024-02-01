---
id: tsaf1iyyeds2nigbxrbls7k
title: 13_WhiteSpaces
desc: ''
updated: 1704123009567
created: 1699969186396
---

Lets look at example #4 now. 
Here is our input file. 
The strings with the green check mark on the left, are the strings that I want to select. 
The strings with the red cross mark on the left, are the strings that I want to exclude. 
All strings start with foo and end in bar with something in between. 
Lets look out for patterns which make the green ones differ from the red ones. 
All the green ones have spaces in between the foo and the bar. 
The red ones have some character other than a space in between the foo and the bar. 
The first green one has 3 whitespaces in between. 
The second green one has 1 whitespace in between. 
The third green one has 6 whitespaces in between. 
The fourth and final green one does NOT have any whitespaces. 
So we can say zero whitespaces between foo and bar. 
Now that we have made our observations, lets move to the next step. 
We have picked the 4 green ones alone, and written them out in a separate list here. 
We have a pair of imaginary dotted line vertical separators which divide each string into 3 different sections. 
The first section is foo, which is fixed, and common to all strings The last section is bar, which is again fixed, and common to all strings. 
The middle section is the variable pattern. 
We have only spaces in the middle section. 
Spaces would not have been visibile to you, so I have put in a text placeholder which calls out the number of spaces. 

So we have 3,1,6 and 0 whitespaces. 
To represent this pattern, lets look at a new regex symbol ,backslash 's'. 
backslash s represents a single whitespace. 
I'll stress on the word 'single'. 
But in our example we have varying number of whitespaces, we have 3, 1 , 6 and zero. 
So we'll combine backslash 's' with star. 
So what would that mean? It would mean , zero or more occurences of whitespace. 
So backslash star could represent 3 whitespaces, one whitespace, 6 , zero whitespaces or any number of whitespaces. 

Lets make use of this new regex symbol and use it in our final step. 
In our final step, we have to come up with a single regex pattern that can represent all the 4 green strings. 
So we will write foo first, then backslash 's' star , then bar. 
So our final regex pattern for example#4 is ready. 
Lets try it out using a regex engine. 
Lets bring up our Linux terminal. 
First, lets see our input file. 
so cat input file name there it is... 
now grep ... 
the regex pattern in single quotes. 
..and then the input file name there you go Compare this list with what you saw in the graphic for this example. 
Only the green ones should have got matched. So that's it for this session, lets move on to the next one.