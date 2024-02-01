---
id: wggdap0lczid1x235s96y5j
title: regex
desc: ''
updated: 1704123658923
created: 1699968247512
---

## Regex

### Use Cases
- Password Verification
- Word Search

To represent this pattern, lets look at a new regex symbol, which is a dot or a period. 
So in regex language, a period is a wildcard that represents exactly one character in a single position. 
It will match ANY character in one position. 

So the pattern is any number of any letters. 
To represent this pattern, we will use a combination of the 2 regex symbols we have seen in the previous examples , a period and a star So in regex language, a dot star represents zero of more occurence of a wildcard.... 
i.e....zero or more occurence of any letter. 
So it can represent abc or bxc or simply z. 
the count as well as the letters do not matter 

So we have 3,1,6 and 0 whitespaces. 
To represent this pattern, lets look at a new regex symbol ,backslash 's'. 
backslash s represents a single whitespace. 
I'll stress on the word 'single'. 
But in our example we have varying number of whitespaces, we have 3, 1 , 6 and zero. 
So we'll combine backslash 's' with star. 
So what would that mean? It would mean , zero or more occurences of whitespace. 
So backslash star could represent 3 whitespaces, one whitespace, 6 , zero whitespaces or any number of whitespaces. 