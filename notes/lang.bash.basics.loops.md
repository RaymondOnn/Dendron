---
id: mkrdhns5lbmskmsj4e69yrv
title: loops
desc: ''
updated: 1702340455802
created: 1697012585505
---

## For Loops

- General Structure

    ``` raw
    for VARIABLE_NAME in ITEM_1 ITEM_N
    do 
        command 1
        command 2
        command N
    done
    ```

``` bash
for current_number in 1 2 3 4 5 6 7 8 9 10
do
    echo $current_number
    sleep 1
done
echo "This is outside of the for loop"

# simplying the code
for n in {1..10}
do
    echo $n
    sleep 1
done
echo "This is outside of the for loop"
```

- It's also a common practice for the list of items to be stored in a variable as in this example.

``` bash
# storing lists in variable

colors="red green blue"
for color in $colors
do
    echo "Color: $color"
done
```

### A more useful example

``` bash
# create compressed version of each log files
for file in logfile/*.log
do 
    tar -czvf $file.tar.gz $file
done
```

## While Loops

- A while loop is a loop that repeats a series of commands for as long as a given condition is true.
  - This condition could be some sort of test
    - for e.g. check if a variable has a certain value or check if a file exists
  - The condition could also be any command.
    - As long as the command succeeds, the while loop will continue. Else the while loop stops.

- General structure

    ``` bash
    while [ CONDITION_IS_TRUE ]
    do
        # commands that change the condition
        command 1
        command 2
        command N
    done 
    ```

``` bash
# initialize variable myvar
myvar=1

# testing for condition: myvar <= 10
while [ $myvar -le 10 ]
do 
    echo $myvar
    myvar=$(( $myvar +1 ))   # add one to myvar
    sleep 0.5
done
>>> 1
>>> 2
>>> 3
>>> 4
>>> 5
>>> 6
>>> 7
>>> 8
>>> 9
>>> 10
```

### Another example

``` bash
# create testfile
touch testfile

# initialize variable myvar
myvar=1

# testing for condition: myvar <= 10
while [ -f ~/testfile ]
do 
    echo "The test file exists."
    sleep 5  # sleep for 5 secs
done
echo "The file no longer exists. Exiting..."
>>> The test file exists.
>>> The test file exists.
>>> The test file exists.
...
# continue printing ""The test file exists.""

rm testfile
# while loop exited after testfile is removed
>>> The file no longer exists. Exiting...
```

### Infinite Loops

- If the condition is always true, then the while loop will never exit.
- To stop, you can hit ctrl-c to interrupt the script if running it interactively from the command line.
- Alternatively, can use the kill command to kill the process.
- Useful for creating some type of daemon that would keep running in the background until it was killed.

``` bash
while true   # condition will always return true
do 
    command N
    sleep 1
done
```

### Loop Control

#### Explicit number of time

- Control the number of times it loops using an index variable i.e. keep checking the value of that variable, and then increment that variable at the end of the command section of the while loop.

``` bash
INDEX=1
while [ $index -lt 6 ]
do
    echo "Creating project-${INDEX}"
    mkdir /usr/local/project-${INDEX}
    ((INDEX++))   # ++: increment operator, keeps adding one to variable each time.
done    
```

#### User Input

``` bash
# If the user answers anything other than a lowercase Y, loop repeats.
while [ "$correct" != "y" ]
do
    read -p "Enter your name: " NAME  # user input for $name
    read -p "Is ${NAME} correct ?" CORRECT # user input for $correct
done
```

#### Command exit status

``` bash
# While server is pingable, condition=true and loop continues.
while ping -c 1 appl >/dev/null
do 
    echo "appl still up..."
    sleep 5
done
echo "appl down, continuing."

```

### Reading files, line by line

If you've ever tried to use a for loop to read a file line-by-line, you'll quickly find that it doesn't work.

- When using a for loop to read a file line-by-line, generally what happens is that the for loop will read the file word-by-word.
- To actually read a file line-by-line, use a while loop together with a read command.
- After the keyword "done", add "<" sign, followed by the file you wish to read.
- Note: You can also read from the output of a command. To do this, pipe the output of the command into the while loop.

``` bash
line_num=1
while read line     # "while read" in order to read line by line
do 
    echo "${line_num}: ${line}" # print line number and line.
    ((line_num++))
done < /etc/fstab   # place file to be read here
```

``` bash
# extract the lines containing "xfs" vua `grep`
# then pipe extracted lines to the while loop.
grep xfs /etc/fstab | while read line
do
    echo "xfs: ${line}"
done
```

- the read command also supports splitting the data it reads into multiple variables.
- Each variable supplied to the read command will store one word or one field of data, with any leftover words or fields assigned to the last variable supplied to the read command.

``` bash
fs_num=1
grep xfs /etc/fstab | while read FS MP REST # FS:1st word, MP: 2nd word, REST: the rest
do
    echo "${fs_num}: file system: ${FS}"
    echo "${fs_num}: mount point: ${MP}"
    ((fs_num++))   # line_counter
done
```

### break and continue

#### `break`

- If you want to exit a loop before its normal ending, use the break statement.
- The break statement exits the loop, but it does not exit the script.
- The script will continue after the loop.

``` bash
# simple menu
while true  # infinite loop
do 
    read -p "1: Show disk usage. 2: Show uptime. " CHOICE
    case "$CHOICE" in
        1) 
            df -h
            ;;
        2) 
            uptime
            ;;
        *)
            break
            ;;
    esac
done
```

Here's one way you could create a simple menu.
This example creates an infinite loop using "while true".
Next, it asks the user for some input and stores that input in the variable CHOICE.
A case statement is used to determine what action to take based on the user's input.
If 1 was entered, then the df command is executed and the loop repeats, asking the user for input again.
If 2 is entered, then the uptime command is executed and the loop repeats.
If anything other than 1 or 2 is entered, then the break statement is executed, which ends the while loop.
Note that the break statement can be used with other kinds of loops, like for loops, for example.

#### `continue`

- if you want to restart the loop at the next iteration before the loop completes, use the continue statement.
- Any commands that follow the continue statement in the loop will not be executed.
- Execution continues back at the top of the loop and the while condition is examined again.

- Like the break statement, the continue statement can be used with other types of loops.
``` bash
# -B option to MySQL disables the ASCII table output that MySQL normally displays.
# -N option suppresses the column names in the output and prevents the header from being displayed.
# -e option causes MySQL to execute the commands that follow it.
mysql -BNe 'show databases' | while read DB # read assign input into $DB
do
    db-backed-up-recently $DB
    if [ "$?" -eq "0" ] # check if current db backed up recently
    then
        continue        # if yes, move on the next db
    fi
    backup $DB          # else, go ahead and backup
done

```

