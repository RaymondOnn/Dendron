---
id: cch4r1tbe0hbfy46mpgg2bj
title: decision_making
desc: ''
updated: 1694739875544
created: 1694739838027
---
## if Statements

#### Equality Operators

- `eq`: equal
- `-ne`: not equal
- `-gt`: greater than

Note: `[]` is the same as the `test` command which check an expression is true or false

``` bash
mynum=200
if [ $mynum -eq 200 ]
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