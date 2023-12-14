---
id: rp0rn52q9hngzr8vut48buf
title: sed
desc: ''
updated: 1702340859531
created: 1699253109405
---


## `sed`: the stream editor command

### What is `sed`

- `S`tream `ED`itor from filtering and transforming text
  - A stream as the data that travels:
    - from one process to another through a pipe
    - from one file to another as a redirect
    - from one device to another.
  - for e.g.
    - standard input = standard input stream
    - standard output = standard output stream
    - standard error = standard error stream.
  - Streams are typically textual data.
- General structure:

  ``` bash
  sed [OPTIONS]... {script-only-if-no-other-script} [INPUT_FILE]...
  ```

- `sed` is a standalone utility, not a Shell built-in.

  ``` bash
  type -a sed
  >>> sed is /usr/bin/sed
  ```

- for standalone commands like you can use man to get help and documentation on how to use that command i.e. `man sed`


### Use Cases

- The sed command performs basic text transformations on streams.
- For e.g.
  - Substitute some text for other text
  - remove lines
  - append text after given lines
  - insert text before certain lines.
- sed is used programmatically, not interactively.

### Find and Replace

- Here is the general structure to do a find and replace

  ``` bash
  # `s` represents the substitute command
  sed 's/<SEARCH_PATTERN>/<REPLACEMENT_TEXT>/<FLAGS' <INPUT_FILE>
  ```

  - the `s` represents the substitute command
  - the search pattern uses regular expression to find matches
  - Delimiters are used to separate the different portions. Any characters can be used as delimiters

    ``` bash
    # GOAL: change /home/jason to be /export/users/Jasonc.

    echo '/home/jason'
    >>> /home/jason

    # method 1: escape the '/' character. Easy to make mistakes 
    echo '/home/jason' | sed 's/\/home\/jason\/\/export\/users\/jasonc/'
    >>> /export/users/jasonc

    # method 2: Use other characters as the delimiter
    echo '/home/jason' | sed 's#/home/jason#/export/users/jasonc#'
    >>> /export/users/jasonc
    echo '/home/jason' | sed 's:/home/jason:/export/users/jasonc:'
    >>> /export/users/jasonc

    ```

- Note that the file contents are unaltered

#### Implementation

  ``` bash
  echo 'I love my wife.' > love.txt
  cat love.txt
  >>> I love my wife.
  sed 's/my wife/sed/' love.txt
  >>>  I love sed
  ```

#### `i` for case `I`nsensitive

- By default, `sed` is case sensitive
- Use `i` flag turn off case sensitivity

  ``` bash
  sed 's/MY WIFE/sed/' love.txt  # sed is case sensitive
  >>>  I love my wife.
  sed 's/MY WIFE/sed/i' love.txt # 'i' flag turn off case sensitivity
  >>>  I love sed.
  cat love.txt  # file contents untouched
  >>> I love my wife.

  echo 'This is line 2.' >> love.txt # '>>' appends line to file
  echo 'I love my wife with all of my heart.' >> love.txt
  cat love.txt
  >>> I love my wife.
  >>> This is line 2.
  >>> I love my wife with all of my heart.
  sed 's/my wife/sed/' love.txt
  >>> I love sed.
  >>> This is line 2.
  >>> I love sed with all of my heart.
  ```

#### `g` for `G`lobal replace

- By default, `sed` just replaces the first occurrence of the search pattern on a line.
- Use `g` flag to override this behaviour.
- Think of `g` flag for global replace.

  ``` bash
  echo 'I love my wife and my wife loves me. Also, my wife loves the cat.' >> love.txt
  cat love.txt
  >>> I love my wife.
  >>> This is line 2.
  >>> I love my wife with all of my heart.
  >>> I love my wife and my wife loves me. Also, my wife loves the cat.
  sed 's/my wife/sed/' love.txt
  >>> I love sed.
  >>> This is line 2.
  >>> I love sed with all of my heart.
  >>> I love sed and my wife loves me. Also, my wife loves the cat.
  sed 's/my wife/sed/g' love.txt
  >>> I love sed.
  >>> This is line 2.
  >>> I love sed with all of my heart.
  >>> I love sed and sed loves me. Also, sed loves the cat.
  ```

#### 'N' to replace the Nth occurrence

- Use the number N as a flag to replace the Nth occurrence of the search pattern

  ``` bash
  sed 's/my wife/sed/2' love.txt
  >>> I love my wife.
  >>> This is line 2.
  >>> I love my wife with all of my heart.
  >>> I love my wife and sed loves me. Also, my wife loves the cat.
  ```
  
#### Saving the changes

- To save the changes in another file, redirect the output of the `sed` command to a new file.

  ``` bash
  sed 's/my wife/sed/g' love.txt > my-new-love.txt
  cat my-new-love.txt   # changes saved to new file
  >>> I love sed.
  >>> This is line 2.
  >>> I love sed with all of my heart.
  >>> I love sed and sed loves me. Also, sed loves the cat.
  cat love.txt      # original file contents remain unaltered
  >>> I love my wife.
  >>> This is line 2.
  >>> I love my wife with all of my heart.
  >>> I love my wife and my wife loves me. Also, my wife loves the cat.
  ```

- There's also the option to save over the original file
  - Use `sed`'s `-i` option for in-place editing
  - To create a backup copy before doing so, add `.bak` behind the `-i`
  - Note to NOT leave a space after the `-i` otherwise it will error i.e. `sed -i.bak ...`

  ``` bash
  # in-place editing: use sed -i option
  # backup original file: add .bak behind the '-i'
  # Note: -i .bak will cause an error i.e. no space after -i option
  sed -i.bak 's/my wife/sed/' love.txt
  ls  # a backup copy, love.txt.bak, was created before the operation
  >>> love.txt love.txt.bak my--new-love.txt
  cat love.txt     # sed has done an in-place edit
  >>> I love sed.
  >>> This is line 2.
  >>> I love sed with all of my heart.
  >>> I love sed and my wife loves me. Also, my wife loves the cat.
  cat love.txt.bak     # looking at the backup copy
  >>> I love my wife.
  >>> This is line 2.
  >>> I love my wife with all of my heart.
  >>> I love my wife and my wife loves me. Also, my wife loves the cat.
  ```

- Use the `w` to save only the changed lines  

  ``` bash

  # w flag + file name>: Save lines when matches were made
  sed 's/my wife/sed/gw like.txt' love.txt
  >>> I love sed.
  >>> This is line 2.
  >>> I love sed with all of my heart.
  >>> I love sed and my wife loves me. Also, my wife loves the cat.
  cat like.txt
  >>> I love sed.
  >>> I love sed with all of my heart.
  >>> I love sed and my wife loves me. Also, my wife loves the cat.
  ```

#### Command pipelining

- `sed` can be used in a pipeline instead of specifying a file to work on
- To `cat` a file and `pipe` the output to `sed` like this: `cat like.txt | sed 's/my_wife/sed/g'` is equivalent to doing this: `sed 's/my_wife/sed/g' like.txt`
- This is a very common pattern with Linux commands where they can be given a file to operate on or they can use the data sent through a pipe to operate on such as cut, arc, sort, unique and others.
- Command pipelining is very powerful because you string as many commands together is needed to make the data look the way you want it to.

#### Use Cases

- templates or template files for e.g. having to use the same configurations over and over again, except for one or two fields
- migrating from one server to another or when you are using a restore of one server to create another new server.
- copying configuration for a given service from one host to another.

### Removing lines

- To removing lines with sed can be done via the `d` command.

  ``` bash
  cat love.txt
  >>> I love my wife.
  >>> This is line 2.
  >>> I love my wife with all of my heart.
  >>> I love my wife and my wife loves me. Also, my wife loves the cat.
  sed '/This/d' love.txt
  >>> I love my wife.
  >>> I love my wife with all of my heart.
  >>> I love my wife and my wife loves me. Also, my wife loves the cat.
  sed '/love/d' love.
  >>> This is line 2.
  ```

### Multiple `sed` Commands

- for e.g. Removing comments and blank lines

  ``` bash
  echo '#User to run service as.' > config
  echo 'User apache' >> config
  echo >> config
  echo '# Group to run service as.' >> config
  echo 'Group apache' >> config
  cat config
  >>> #User to run service as.
  >>> User apache
  >>> 
  >>> # Group to run service as.
  >>> Group apache

  # Step 1: Removing comments
  # Note: Caret symbol in regex indicate the start of the line
  sed '/^#/d' config   
  >>> User apache
  >>> 
  >>> Group apache

  # Step 2: Removing blank lines
  # '$' in regex indicate the end of the line
  # Hence '^$' matches blank line
  sed '/^$/d' config   
  >>> #User to run service as.
  >>> User apache
  >>> # Group to run service as.
  >>> Group apache

  # Combining both steps
  sed '/^#/d ; /^$/d' config
  >>> User apache
  >>> Group apache
  sed '/^#/d ; /^$/d ; s/apache/httpd/' config
  >>> User httpd
  >>> Group httpd
  # Alternatively, 
  sed -e '/^#/d' -e '/^$/d' -e 's/apache/httpd/' config
  
  ```

- `sed` also allows you to specify a file containing the sed commands.

  ``` bash
  echo '/^#/d' > script.sed 
  echo '/^$/d' >> script.sed
  echo 's/apache/httpd/' >> script.sed
  cat script.sed
  >>> /^#/d
  >>> /^$/d
  >>> s/apache/httpd/ 
  sed -f script.sed config
  >>> User httpd
  >>> Group httpd
  ```

### Using Addresses

- An address determines on what lines the `sed` command will be executed on.
- If no address is given, the command is performed on all lines.
- An address is specified before the `sed` command.
- The simplest of addresses is a line number.

  ``` bash
  cat config
  >>> #User to run service as.
  >>> User apache
  >>> 
  >>> # Group to run service as.
  >>> Group apache
  
  # the '2' indicate execution for line two only
  sed '2 s/apache/httpd/' config   # Same as sed '2s/apache/httpd/' config 
  >>> #User to run service as.
  >>> User httpd
  >>> 
  >>> # Group to run service as.
  >>> Group apache
  
  # Regex can be used for addresses to select lines
  # Here, execution on lines that contain the word 'Group'
  sed '/Group/ s/apache/httpd/' config   # Same as sed '2s/apache/httpd/' config 
  >>> #User to run service as.
  >>> User apache
  >>> 
  >>> # Group to run service as.
  >>> Group httpd

  # Here, execution on lines 1 through 4
  sed '1,3 s/run/execute/' config  
  >>> #User to execute service as.
  >>> User apache
  >>> 
  >>> # Group to execute service as.
  >>> Group apache

  # Here, execution from line that matches '#User' to the next blank line.
  sed '/#User/ ,/^$/ s/run/execute/' config
  >>> #User to execute service as.
  >>> User apache
  >>> 
  >>> # Group to run service as.
  >>> Group apache
  ```

In this lesson you learned the most common use case of sed which is to perform text substitutions.
You learned how to replace specific occurrences as well as how to replace all occurrences of the surge pattern.
In addition to finding and replacing text, you learned how to delete text with the d command.
From there you learned how to save the alterations performed by sed as well as how to make backups of the original file so that your data is safe.
Next, you learned three different ways to execute multiple sed commands on the same set of data.
Finally, you learned how to use addresses to work on very specific sections of input.
