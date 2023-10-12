---
id: mkrdhns5lbmskmsj4e69yrv
title: loops
desc: ''
updated: 1697016931222
created: 1697012585505
---

## For Loops

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

### A more useful example

``` bash
# create compressed version of each log files
for file in logfile/*.log
do 
    tar -czvf $file.tar.gz $file
done
```

## While Loops

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
