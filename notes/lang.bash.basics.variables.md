---
id: c50jfpiu057q5z934e66wad
title: variables
desc: ''
updated: 1702340464797
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

---

Positional parameters are variables that contain the contents of the command line. The variables are $0 through $9. The script itself is stored in $0, the first parameter is stored in $1, the second in $2, and so on. Let's take this command line as an example. The contents of $0 are "script.sh", $1 contains "parameter1", $2 contains "parameter2", and $3 contains "parameter3". This script called, archive_user.sh,
accepts a parameter which happens to be a username.

Anything that follows the pound sign is a comment.

The only exception to this is the shebang on the first line.

Everywhere else in the script where a pound sign is encountered

it marks the beginning of a comment.

Comments are dutifully ignored by the interpreter

as they are for the benefit of us humans.

Anything that follows the pound sign is ignored.

If a pound sign starts at the beginning of a line

the entire line is ignored.

If a pound sign is encountered in the middle of a line

only the information to the right of the pound sign is ignored.

Here is what the output looks like

when we execute the script.

Instead of referring to $1 throughout the script,

let's assign its value to a more meaningful variable name.

In this case, let's assign it to the variable called user.

The output remains exactly the same.

You can access all the positional parameters

starting at $1 to the very last one on the command line

by using the special variable $@.

Here is how to update the script

to accept one or more parameters.

Now you can pass in multiple users to the script.

And the for loop will execute for each user

that you supplied on the command line.

Here's what the script will look like

if we pass 'tar' to 2 users.

In this case, 'chet' and 'joe'.

If you want to accept standard input

use the read command.

Remember that standard input typically comes

from a person typing at the keyboard,

but it can also come from other sources

like the output of a command in a command pipeline.

The format for the read command is

read -p "PROMPT" VARIABLE_NAME.

This version of the archive_user.sh script

asks for the user account.

In this example, I ran the script

then typed in the username 'mitch'
---

Let's review.

The first line in a shell script

should start with a shebang

and the path to the interpreter

that should be used to execute the commands listed in the script.

To assign a value to a variable

start with the variable name

followed by an equals sign

then followed by the value.

Do not use a space before or after the equals sign.

You can access the value stored in a variable by using

$VARIABLE_NAME or $.

The latter form is required if you want to preceed

or follow the variable with additional data.

To assign the output of a command to a variable

enclose the command in parentheseis

and precede it with a dollar sign.

Perform tests by placing expressions in brackets.

Tests are typically combined with if statements.

Use if, if/else, or if/elif/else statements

to make decisions in your scripts.

To perform an action or series of actions

on multiple items, use a for loop.

To access items on the command line

use positional parameters.

The name of the program is represented by $0,

the first parameter is represented by $1, and so on.

To access all the items on the command line

starting at the first parameter ($1),

use the special variable $@.

You can place comments in your scripts

by using the pound sign.

If you like to accept user input in your scripts,

use the read command.
