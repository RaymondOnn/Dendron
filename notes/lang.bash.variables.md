---
id: c50jfpiu057q5z934e66wad
title: variables
desc: ''
updated: 1697018431097
created: 1694737023808
---

## Variables

#### Basics: Creating and using variables

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
```

#### SubShell: Storing output in variables

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

