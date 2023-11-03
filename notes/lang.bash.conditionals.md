---
id: cch4r1tbe0hbfy46mpgg2bj
title: conditionals
desc: ''
updated: 1698880197343
created: 1694739838027
---
## Conditionals

### Tests

- To create a test, place a conditional expression between brackets.
- The syntax is: [ condition-to-test-for ].
- If you are using the bash shell, you can run the command 'help test' to see the various types of tests you can perform.
- You can also read the man page for test by typing in 'man test'.
- Here are of some of the more common tests you can perform.
  - `-d`: is to see whether what follows is a directory.
  - `-e`: returns true if the file exists.
  - `-f`: returns true if the file exists and it is a regular file. For example, it's not a symlink.
  - `-r`: returns true if the file is readable by you.
  - `-s`: returns true if the file exists and it's not empty.
  - `-w`: tests to see if the file is writable.
  - `-x`: tests to see if the file is executable.
  - `-z`: followed by a string returns true if the string is empty.
  - `-n`: will return true if the string is not empty.

#### Equality Operators

- If you place 2 strings on either side of an equals sign, then it will return true if those strings are equal.
- You can also test the 2 strings are not equal by using '!' before the equals sign.
- You can also do number comparisons like
  - '-eq' to test if the numbers are equal.
  - '-ne' if they are not equal.
  - '-lt' if the first number is less than the second number.
  - '-le' if the first number is less than or equal to the second number.
  - '-gt' if the first number is greater than the second number.
  - '-ge' will return true if the first number is greater than or equal to the second number.

### `if` Statements

- General Structure

``` raw
if [condition-is-true]
then
    command N
if [condition-is-true]
then
    command N    
else
    command N
fi    
```

- Note that it's a best practice to enclose variables in quotes to prevent some unexpected side effects when performing conditional tests.

``` bash
mynum=200
if [ "$mynum" -eq 200 ]
then
    echo "The condition is true."
else
    echo "The variable does not equal 200."    
fi    
>>> The condition is true.


mynum=300
if [ ! $mynum -eq 200 ]      # ! acts as NOT
then
    echo "The condition is true."
else
    echo "The variable does not equal 200."    
fi    
>>> The condition is true.
```

#### Is Exists

- `-f`: check for a file
- `-d`: check for a directory / folder

``` bash
# checking for a file named myfile
if [ -f ~/myfile ]      # ~ indicates home directory
then 
    echo "The file exists"
else
    echo "The file does not exists"
fi    
>>> The file does not exists

touch myfile            # create file
if [ -f ~/myfile ]      
then 
    echo "The file exists"
else
    echo "The file does not exists"
fi    
>>> The file does not exists

rm myfile               # delete file
if [ -f ~/myfile ]      
then 
    echo "The file exists"
else
    echo "The file does not exists"
fi    
>>> The file does not exists
```

#### The `which` command

- The `which` command locates an executable file in your shell’s search path

```bash
command = /usr/bin/htop     # path to the executable file

if [ -f $command ]          # file check
then
    echo "$command is available, let's run it..."
else
    echo "$command is NOT available, installing it..."    
    sudo apt update && sudo apt install -y htop
fi    

$command      # run file

# ------------- ALTERNATIVELY ----------------------

command=htop     # path to the executable file

# 'command' builtin command that check if a command is available
# running an actual command here, instead of testing an expression      
if command -v $command     
then
    echo "$command is available, let's run it..."
else
    echo "$command is NOT available, installing it..."    
    sudo apt update && sudo apt install -y $command
fi    

$command      # run file

```

### `case` Statements

- general structure:

  ``` bash
    case "$var" in
        pattern_1)
            # commands go here
            ;;
        pattern_N)
            # commands go here
            ;;   
        *)  # wildcards matches anything, can be use for default cases  
            # commands go here
            ;; 
    esac  # case spelled backwards
  ```

- Pattern matching is used to compare against multplie values  
  - the patterns are case sensitive.
  - Pattern can include wildcards that can act as a catch-all
  - To execute the same block of code for multiple patterns, separate the patterns with a pipe.
- alternative to if statements comparing same variables to different values

  ``` bash
  if [ "$VAR"="one" ]
  elif [ "$VAR"="two" ]
  elif [ "$VAR"="three" ]
  elif [ "$VAR"="four" ]
  ```

- may be easier to read than complex if statements

``` bash
case "$1" in
    start|START)  # "|" operator implies OR
        /usr/sbin.sshd
        ;;
    stop|STOP)
        kill $(cat /var/run/sshd.pid)
        ;;
    *)  # default case, a catch-all
        echo "Usage: $0 start|stop" ; exit 1
        ;;    
esac
```

``` bash
read -p "Enter y or n: " ANSWER
case "$ANSWER" in
    [yY] | [yY][eE][sS]) # character classes
        echo "You answered yes."
        ;;
    [nN] | [nN][oO])
        echo "You answered no"
        ;;
    *)
        echo "Invalid answer."
        ;;
esac        
```
