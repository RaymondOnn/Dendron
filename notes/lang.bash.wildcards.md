---
id: 0aj7k2y0zb824r00u4e520r
title: wildcards
desc: ''
updated: 1698885636895
created: 1698885630747
---


### Wildcards

- A wildcard is a character or a string that is used to match file and directory names
- Used to create search patterns that will return a list of matching files and directories i.e  glob patterns.s
- Wildcards can be used with most Linux commands i.e commanda (such as ls, rm, cp, mv) that accepts a file/directory as argument
- Here are the 2 main wildcards
  - `*`: matches zero or more characters, it matches anything
  - `?`: matches exactly 1 character

#### Escaping wildcards: matching for '*' and '?'

- To escape the wildcard, simply place the \ before the wildcard character
- If you wanna make your life easier, don't name your files with question marks and asterisks
- For example, `*\?`: strings that end with a question mark i.e. 'done?'

### Character classes

- Used to create very specific search patterns
- General structure

  ```bash
  [CHARS_TO_MATCH]
  ```

- To exclude characters in a match, use an exclamation mark
- For example,
  - `[aeiou]`: strings that is one character long and a vowel
  - `ca[nt]*`: strings that start with 'ca' +  'n' | 't' + zero or more characters for e.g. can, cat, candy and catch
  - `[!aeiou]*`: strings that do NOT start with vowels for e.g. 'baseball' and 'cricket'

#### Ranges

- Create a range by separating two characters with a hyphen
If you want to match all the characters from a to g, use [a-g]
If you want to match the numbers - 3, 4, 5 and 6, use [3-6]

#### Pre-defined ranges

- There are also predefined named character classes
- These named character classes represent the most commonly used ranges
  - `[[:Alpha:]]`:  alphabetic letters i.e. both lower and upper case letters
  - `[[:digit:]]`: numbers and decimal from 0 to 9
  - `[[:alnum:]]`: alphanumeric characters i.e. alpha and digits
  - `[[:lower:]]`: lowercase letters
  - `[[:upper:]]`: uppercase letters
  - `[[:space:]]`: wide space i.e. characters such as spaces, tabs and newline characters