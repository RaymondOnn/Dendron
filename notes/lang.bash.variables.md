---
id: c50jfpiu057q5z934e66wad
title: variables
desc: ''
updated: 1698687055154
created: 1694737023808
---

## Variables

- Variables are simply storage locations that have a name.

#### Basics: Creating and using variables

- To assign a value to a variable, use the syntax VARIABLE_NAME="Value". Make sure to not use spaces before or after the equals sign.
- To use a variable, precede the variable name with a dollar sign.
- Variable names can contain letters, digits, and underscores.
- They can start with letters or underscores, but cannot start with a digit.
- Variables are case sensitive, and by convention variable names are in all uppercase.

``` bash
# create variable
# Variables only persist within a session!!
myname="Jay"
echo myname
>>> myname

echo $myname     # Note: '$' needed when referencing variables
>>> Jay
```

#### Use of variables with Quotes

``` bash
# Note the use of variable within the double quotes
echo "Hello, my name is $myname."
>>> Hello, my name is Jay.

echo 'Hello, my name is $myname.'
>>> Hello, my name is $myname.

MY_SHELL = "bash"
echo "I am ${MY_SHELL}ing on my keyboard."
>>> I am bashing on my keyboard.
```

#### SubShell: assign output to variables

- You can also assign the output of a command to a variable.
- To do this, enclose the command in parenthesis and precede it with a dollar sign.

```bash
# $() is called a sub shell
# it allows execution of a command in the background and stored it somewhere
files=$(ls)
echo $files

files=$(pwd)
echo $files
>>> /home/jay
```

#### System / Environment Variables

- Uppercase variables meant for system / environment variables. Lowercase variables for all other variables
- To see all environment variables, use the command `env`

```bash
name="Jay LaCroix"
now=$(date)
echo "Hello $name"
echo "The system time and date is:"
echo $now
echo "Your username is: $USER"
>>> Hello Jay LaCroix
>>> The system time and date is:
...
>>> Your username is: jay
```
