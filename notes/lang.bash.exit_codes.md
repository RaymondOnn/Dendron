---
id: 6bf69nc5iojcsb4agwypwki
title: exit_codes
desc: ''
updated: 1698711988453
created: 1697010068785
---
## Exit Codes

- Every time a command is executed it returns an exit status.
- The exit status, which is sometimes called a return code or exit code, is an integer ranging from 0 to 255.

### Interpreting Exit Codes

- By convention, commands that execute successfully return a 0 exit status.
- If some sort of error is encountered, then a non-zero exit status is returned.
- If you want to find what the various exit statuses mean, you have to consult the documentation for the given command or look at its source code.
- You can use the 'man' command and info commands to read the documentation for most commands on your system.

### Using Exit Codes

- These return codes can be used in your script for error checking for e.g. checking for a zero return code or checking for a specific error code.
- You can use the exit status of a command to make a decision or perform a different action based on the exit status.

#### The `$?` variable

- The special variable $? contains the return code of the previously executed command.

    ``` bash

    ls -l
    # ? contains the status of the previous command i.e. ls -l in this case
    echo $?
    >>> 0    

    # trying on an invalid directory
    ls -l /misc
    >>> ls: cannot access '/misc': Non such file or directory
    echo $?
    >>> 2
    ```

- We can assign the return code of a command to a variable and then use that variable later in the script.

  ``` bash
  # Here exit code output is stored in $return_code
  host="google.com"
  ping -c 1 $host
  return_code=$?

  if [ "$return_code" -ne "0" ]
  then
      echo "$host unreachable."
  fi  
  ```

>**Note: When you check for exit code matters!!**

``` bash
directory=/notexist
# -d: checking for directory
if [ -d $directory ]
then 
    echo $?
    echo "The directory $directory exists."
else
    echo $?
    echo "The directory $directory doesn't exists."
fi
echo "The exit code for this script run is $?." 
>>> 1   # why? this exit code was for the search for directory
>>> The directory /etc exists.
>>> The exit code for this script run is 0 # the exit code was for the echo command
```

#### Using exit codes within a if-else statement

``` bash
sudo apt remove htop
package=htop     
sudo apt install $package
if [ $? -eq 0 ]   # if exit code is not 0
then 
    echo "The installation of $package was successful."
    echo "The new command is available here:"
    whick $package
else 
    echo "$package failed to install."
fi
>>> The installation of htop was successful.
>>> The new command is available here:
>>> /usr/bin/htop

# Note: you can redirect output to other locations e.g. a log file
sudo apt remove htop
package=htop    

# >>: a redirect, here output is redirected into a .log file
sudo apt install $package >> package_install_results.log
if [ $? -eq 0 ]   # if exit code is not 0
then 
    echo "The installation of $package was successful."
    echo "The new command is available here:"
    which $package
else 
    echo "$package failed to install." >> package_install_failre.log
fi
>>> The installation of htop was successful.
>>> The new command is available here:
>>> /usr/bin/htop
# Output recorded in log file
```

#### Logical ANDs and ORs

- You can use logical ANDs and ORs with exit statuses.
  - The double ampersand (&&) represents AND
  - the double pipe (||) represents OR.
- You can chain together multiple commands with either ANDS or ORS.
- The command following a double ampersand will only execute if the previous command succeeds.i.e. exits with a 0 exit status.

``` bash
# AND example
# 2nd command will only run if 1st is successful (exit code 0)
mkdir /tmp/bak && cp test.txt /tmp/bak


# OR example
# 2nd command will only run if 1st is NOT successful (non-zero exit code)
cp test.txt /tmp/bak || cp test.txt /tmp
```

- If you want to chain multiple commands together on a single line, you can do that by separating those commands with a semicolon.
- The semicolon does not check the exit status of the previous command.
- The command following a semicolon will always get executed, no matter if the previous command failed or succeeded.

``` bash
# No difference between case 1 and case 2
# Case 1
cp test.txt /tmp/bak ; cp test.txt /tmp

# Case 2
cp test.txt /tmp/bak
cp test.txt /tmp
```

### Overwriting the Exit Code

- Not only do normal commands return an exit status, but shell scripts do as well.
- The return code for your entire shell script will depend on the last command that was executed in your shell script (given that the exit command is not used)
- However, You can control the exit status of your shell script by using the `exit` command.
- To do so, use the `exit` command in your script and follow it with a number from 0 to 255.
  - If you do not specify a return code with the exit command, then the exit status of the previously executed command is used as the exit status.
- You can use the exit command anywhere in your shell script.
- Whenever the exit command is reached, your shell script will stop running.

``` bash
sudo apt install notexist
exit 0  # exit script with exit code 0
echo $?
>>> Unable to install package notexist
echo $?
>>> 0


directory=/etc

if [ -d $directory ]
then 
    echo "The directory $directory exists."
    exit 0
else
    echo "The directory $directory doesn't exists."
    exit 1
fi
>>> The directory /etc exists.
```
