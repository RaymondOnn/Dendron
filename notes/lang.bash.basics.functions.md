---
id: 8hesphf53zkti209e93qs3r
title: functions
desc: ''
updated: 1698718876636
created: 1698712078731
---

## Functions

- A function is a block of reusable code that performs an action and returns an exit status or return code.
- A function must be defined before it is called.
- When you call a function, you can pass data into that function.
- You can access that data within your function as parameters.

### Why use functions

- Functions allow you to write a block of code once and use it many times.
  - Reduces script length
  - Easier to maintain since single place to change, test, troubleshoot, and document a given task.

### How to create them

- There are two ways to create a function in the shell script.

#### 1st Method

``` bash
function function-name() {
    # Code goes here
}
```

- Use the keyword function, then follow it by the function's name, and then a set of parenthesis.
- Use an opening curly brace.
- The code or commands that follow will be executed when the function is called.
- To end or close your function, use a closing curly brace.

#### 2nd Method

``` bash
function-name() {
    # Code goes here
}
```

- The second way to declare a function is exactly like the first
except that you do not use the keyword function in your declaration.
- Everything else stays the same.

### How to use them

- After you define a function, it acts like any other command you can use in your shell script.
- To call or execute a function, simply list its name on a line in the script.
- When calling the function, do not use parentheses!!!

``` bash
#!/bin/bash
function hello() {
    echo "Hello!"
}
hello
>>> Hello!
```

- functions can call other functions.
- Works as long as functions are defined before execution, order of definition does not matter
  - In a script, the commands and components are read from the top to the bottom i.e. not pre-compiled
  - It's a best practice to place all of your functions at the top of your script.
  - This ensures that they are all defined before they are used.

``` bash
function hello() {
    echo "Hello!"
    now
}
function now() {
    echo "It's $(date + %r)"
}
hello
```

### Function Parameters

- functions can accept parameters which can accessed using $1, $2, etc.
  - first parameter stored in $1, second param in $2, etc...
- $@ contain all the parameters
- $0 is still the name of the script itself, not the function name.
- To send data to a function, supply the data after the function name.

``` bash
function hello() {
    echo "Hello $1"
}
hello Jason
>>> Hello Jason


function hello() {
    for NAME in $@
    do
        echo "Hello $NAME"
    done
}
hello Jason Dan Ryan
>>> Hello Jason
>>> Hello Dan
>>> Hello Ryan
```

### Variable scope

#### Global Variables

- By default, all variables are global i.e. can be accessed anywhere in the script
- Variables have to be defined before use.
- If a global variable is defined within a function,
it is not available outside that function until the function is called and executed.

``` bash
my_function() {
    global_var=1
}
echo $global_var  # prints blank line
>>>
my_function
# global_var now available -------------
echo $global_var  # prints 1
>>> 1
```

#### Local Variables

- Can only be accessed within the function
- Created using the local keyword i.e. `local LOCAL_VAR=1`
- Only functions can have local variables
- Best practice to keep variables local in functions

### Exit Codes

- Functions have an exit status
- Can be overwritten using `return <RETURN_CODE`
- Else it will be the exit status of the last command executed in the function

``` bash
function backup_file() {
    if [ -f $1 ]  # if param is a file and exits
    then
        # basename command returns just the filename
        # $$ represents the process ID (PID)
        local BACK="/tmp/$(basename ${1}).$(date + %F). $$"
        echo "Backing up $1 to ${BACK}"
        cp $1 $BACK
    else
        # file does not exist (exit code 1)
        return 1
    fi
}
backup_file /etc/hosts
if [ $? -eq 0 ]
then 
    echo "Backup succeeded!"
else
    echo "Backup failed!"
    exit 1 # exit wit non-zero exit code
fi
```
