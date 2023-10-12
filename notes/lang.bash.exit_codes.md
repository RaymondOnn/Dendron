---
id: 6bf69nc5iojcsb4agwypwki
title: exit_codes
desc: ''
updated: 1697013309898
created: 1697010068785
---
## Exit Codes

- Helps to indicate if outcome is success or not


## Interpreting Exit Codes
- Generally, exit code 0 implies success and exit code that are not 0 indicate otherwise

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

### Using Exit Codes

##### Using exit codes within a if-else statement
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

##### When you check for exit code matters!!

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

### Overwriting the Exit Code
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