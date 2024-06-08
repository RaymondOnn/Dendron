---
id: c50jfpiu057q5z934e66wad
title: variables
desc: ''
updated: 1715884962629
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
#### Special Parameters: `$*`, `$@`, `$#`, `$$`, `$!`, `$?`, `$-`, `$_`

##### `$*` and `$@` to Expand Positional Parameters
- Can be used to access the whole list of positional parameters
- Outside of double quotes, these two are equivalent: Both expand to the list of positional parameters starting with `$1` (separated by spaces).
- Within double quotes, they differ: 
    - `$*`: Equivalent to the list of positional parameters, separated by the first character of IFS `$1c$2c$3…`.
    - `$@`: Equivalent to the list of positional parameters, separated by unquoted spaces, i.e., “$1” “$2″..”$N”.
    ``` bash
    # Example 1: Use Bash $* and $@ to Expand Positional Parameters

    # First, create the expan.sh as shown below.
    $ cat expan.sh
    #!/bin/bash

    export IFS='-'

    cnt=1

    # Printing the data available in $*
    echo "Values of \"\$*\":"
    for arg in "$*"
    do
    echo "Arg #$cnt= $arg"
    let "cnt+=1"
    done

    cnt=1

    # Printing the data available in $@
    echo "Values of \"\$@\":"
    for arg in "$@"
    do
    echo "Arg #$cnt= $arg"
    let "cnt+=1"
    done

    # Next, execute the expan.sh as shown below to see how $* and $@ works.

    $ ./expan.sh "This is" 2 3
    Values of "$*":
    Arg #1= This is-2-3
    Values of "$@":
    Arg #1= This is
    Arg #2= 2
    Arg #3= 3
    ```
    - The above script exported the value of IFS (Internal Field Separator) with the ‘-‘.
    - There are three parameter passed to the script `expan.sh`: `$1=”This is”`,`$2=”2″` and `$3=”3″`.
    - When printing the each value of special parameter `$*`, it gives only one value which is the whole positional parameter delimited by IFS.
    - Whereas `$@` gives you each parameter as a separate word.

##### `$#` to Count Positional Parameters
- `$#` is the special parameter in bash which gives you the number of positional parameter in decimal.

``` bash
# Example 2: Use $# to Count Positional Parameters
# First, create the arithmetic.sh as shown below.

$ cat arithmetic.sh
#!/bin/bash

if [ $# -lt 2 ]
then
  echo "Usage: $0 arg1 arg2"
  exit
fi

echo -e  "\$1=$1"
echo -e "\$2=$2"

let add=$1+$2
let sub=$1-$2
let mul=$1*$2
let div=$1/$2

echo -e "Addition=$add\nSubtraction=$sub\nMultiplication=$mul\nDivision=$div\n"

# If the number of positional parameters is less than 2, it will throw the usage information as shown below,

$ ./arithemetic.sh 10
Usage: ./arithemetic.sh arg1 arg2
```
##### `$$` and `$!`: Process related Parameters

- `$$` will give the process ID of the shell. 
- `$!` gives you the process id of the most recently executed background process.

``` bash
# Example 3: Process related Parameters – $$ and $!
# The following script prints the process id of the shell and last execute background process ID.

$ cat proc.sh
#!/bin/bash

echo -e "Process ID=$$"

sleep 1000 &

echo -e "Background Process ID=$!"

# Now, execute the above script, and check the process id which its printing.

$ ./proc.sh
Process ID=9502
Background Process ID=9503
$ ps
  PID TTY          TIME CMD
 5970 pts/1    00:00:00 bash
 9503 pts/1    00:00:00 sleep
 9504 pts/1    00:00:00 ps
$
```


##### Other Bash Special Parameters – `$?`, `$-`, `$_`
- `$?` Gives the exit status of the most recently executed command.
- `$-` Options set using set builtin command
- `$_` Gives the last argument to the previous command. At the shell startup, it gives the absolute filename of the shell script being executed.

- `$_` will give the value after expansion
    - Note that the last echo statement `echo -e $_`  also prints `hB` which is the value of last argument of the previous command.
    ``` bash
    $ cat others.sh
    #!/bin/bash

    echo -e "$_"; ## Absolute name of the file which is being executed

    /usr/local/bin/dbhome  # execute the command.
    #check the exit status of dbhome
    if [ "$?" -ne "0" ]; then
    echo "Sorry, Command execution failed !"
    fi

    echo -e "$-"; #Set options - hB

    echo -e $_  # Last argument of the previous command.

    $ ./others.sh
    ./others.sh
    /home/oracle
    Sorry, Command execution failed !
    hB
    hB
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
