---
id: 3m0xa3l6crahi0uxp6etmyz
title: debug
desc: ''
updated: 1700672726839
created: 1699236638975
---


## Debugging

- The process of finding errors in your script or fixing unexpected behaviors

### Why Debug

- A bug is really an error
- Examine the inner workings of your script
- Determine the root of unexpected behaviour
- Fix bugs

### Built In Debugging Help

#### `-x`: prints commands as they execute

- Instead of variables being displayed, the values of those variables are displayed.
- After substitutions and expansions
- With wildcards, prints everything after any substitutions and expansions are applied.
- this type of debugging called "print debugging," tracing, or an x-trace.
- If you are using this option in a shell script, simply add -x to the end of the shebang line.

  ``` bash
  #!/bin/bash -x
  TEST_VAR="test"
  echo "$TEST_VAR"

  # '+' indicate the commands executed
  >>> + TEST_VAR=test
  >>> + echo test
  >>> test
  ```

- debugging on a section of the code
  - Add a `set -x` line just before the section of code
  - Add a `set +x` line after the section of code

  ``` bash
  #!/bin/bash
  TEST_VAR="test"
  set -x   # debugging starts on next line
  echo "$TEST_VAR"
  set +x   # debugging stops
  hostname
  >>> + echo test
  >>> test
  >>> + set +x
  >>> linuxsvr
  ```

#### `-e`: exit on error

- Causes your script to exit immediately if a command exits with a non-zero exit status.
- Helps to pin-point exactly where the problem is.

  ``` bash
  #!/bin/bash -e
  FILE_NAME="/not/here"
  ls $FILE_NAME
  echo $FILE_NAME
  >>> ls: cannot access /not/here: No such file or directory
  ```

- Can be combined with other options
  - Note options with no arguments can be combined using only one hyphen is required followed by the options and order does not matter.
  - for e.g. `#!/bin/bash -ex`, `#!/bin/bash -xe`, `#!/bin/bash -e -x`,  `#!/bin/bash -x -e`

  ``` bash
  #!/bin/bash -ex
  FILE_NAME="/not/here"
  ls $FILE_NAME
  echo $FILE_NAME
  >>> + FILE_NAME=/not/here
  >>> + ls /not/here
  >>> ls: cannot access /not/here: No such file or directory
  ```

#### `-v`: prints shell input lines as they are read

- Prints the shell commands just like they are read in from the script.
- Causes every line of the script to be displayed to the screen before it is executed.

  ``` bash
  #!/bin/bash -v
  TEST_VAR="test"
  echo "$TEST_VAR"
  >>> #!/bin/bash -v
  >>> TEST_VAR="test"
  >>> echo "$TEST_VAR"
  >>> test
  ```

- With wildcards, prints everything before any substitutions and expansions are applied.
- When combined with `-x`, you can see what a line looks like before and after substitutions and expansions occur.

  ``` bash
  #!/bin/bash -vx
  TEST_VAR="test"
  echo "$TEST_VAR"
  >>> #!/bin/bash -vx
  >>> TEST_VAR="test"
  >>> + TEST_VAR=test
  >>> echo "$TEST_VAR"
  >>> + echo test
  >>> test
  ```

- Can be combined with other options

#### For more information

- `help set | less`: display information about the options we covered in this lesson plus other options that are available.

### Variables for debugging

- You can create your own debugging code
- Use a special variable for e.g `DEBUG=true`
- You can use this type of pattern to skip the execution of certain commands when DEBUG is set to true.

  ``` bash
  DEBUG=true
  if $DEBUG
  then
    echo "Debug mode ON."
  else
    echo "Debug mode OFF."
  fi
  ```

- You can use a `DEBUG` variable in conjunction with ANDS or ORs.

  ``` bash
  # AND case
  DEBUG=true
  $DEBUG && echo "Debug mode ON."
  
  # OR case
  DEBUG=false
  $DEBUG || echo "Debug mode OFF."
  ```

- Another thing you can do is set the value of the DEBUG variable to echo.

  ``` bash
  DEBUG="echo"
  $DEBUG ls
  >>> ls

  # Here, ls command will execute instead
  # DEBUG="echo"
  $DEBUG ls  
  ```

- You can even create your own debug function and add whatever you would like around each command, or choose NOT to execute the command if you wish.

  ``` bash
  debug () {
    echo "Executing:$@"
    $@
  }
  
  debug ls
  
  ```

### Manual debugging tips

- Open up a second terminal
- Copt and paste the commands into the terminal
- Can be helpful to use `set -x` on the command line

### Syntax Highlighting

- Syntax errors are common i.e. typos, missing brackets, missing quotes etc
- Helps to use an editor with syntax highlighting

### More Bash built-ins

#### The PS4 variable

- The environment variable that is valuable for shell script debugging is PS4.
- Controls what is displayed before a line when using the `-x` option
- The default is "+". However, we can explicitly set the PS4 variable.
- Bash builtin variables:
  - `BASH_SOURCE`: name of script
  - `LINENO`: line number in the script

``` bash
#!/bin/bash
PS4='+ $BASH_SOURCE : $LINENO : '
TEST_VAR="test"
echo "$TEST_VAR"
>>> + PS4='+ $BASH_SOURCE : $LINENO : '
>>> ./test.sh : 3 : TEST_VAR=test
>>> ./test.sh : 4 : echo test
>>> test

#!/bin/bash
PS4='+ ${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]}()'
debug () {
    echo "Executing: $@"
    $@
}
debug ls
>>> + ./test.sh:4:debug(): ls
```

### File Types

#### DOS vs Linux File Types

- CRLF / Carriage Return, Line Feed
- `cat -v script.sh` to display non-printable characters, like carriage returns.

``` raw
#!/bin/bash^M
# This file conains carriage returns.^M
echo "Hello world."^M
```

Plain text files, like we are working with for shell scripts, contain a control character to represent the end of a line.
For Unix and Linux systems, the control character representing a new line is a Line Feed.
DOS or Windows systems actually use two characters to represent a new line: a carriage return and a line feed.
If you've ever created a text file on a Linux system and sent it to someone who is using a Windows system, it may very well display as one long line on their computer.

This is due to the lack of carriage returns.
If you were to do the opposite, say, create a text file on a Windows system and open it on a Linux system, there will be additional characters, specifically carriage returns, in that file.
The problem in this situation is that when you display the contents of the file to the screen, you will not see the additional carriage returns.
For example, if you ran 'cat script.sh' and that file had carriage returns in it, you wouldn't see them.
You will have to run 'cat -v script.sh' to display non-printable characters, like carriage returns.

Carriage returns are represented by the caret symbol followed by M (^M).
If you were to execute a file that contains carriage returns, you'll probably run into an error like this: /bin/bash^M:, No such file or directory.
The carriage return is being treated as part of the file name.
In order to get this script to run, we need to remove the carriage returns.

This situation is another reason why you should always start your scripts with a shebang.
If you didn't have the shebang line, the commands would be executed using your current shell.
This can lead to strange things happening.
The script might work as expected in some parts, but totally fail at other parts due to carriage returns.
If you start getting strange errors that make no sense, check to see if there are carriage returns in your script.

In addition to using the 'cat -v' command to see if there are carriage returns in a file, you can also use the file command.
If it finds the additional carriage returns, it will alert you to that fact.
To easily remove these characters, I recommend that you use the 'dos2unix' utility.
It may not be installed by default on your system, so you may have to manually install it if you need it.
This little utility converts a DOS file (or a file with carriage returns) into a Unix file, (or a file without carriage returns).

There is another utility which does the exact opposite, and it's called unix2dos.
After you run the dos2unix command against your file, you'll find that the carriage returns are removed.
You can confirm this by running the file command against the file.
You'll notice there is no warning about CRLF line terminators.
The common ways you end up with these unwanted carriage returns in your shell scripts is due to creating files on a Windows system and uploading those files to a Linux system.

If you want to create shell scripts on a Windows system, see if your editor supports Unix-style line endings.
If it does support this option, simply turn it on.
Also, some editors can convert between DOS and Unix style file types.
You can also end up with unwanted characters in your shell scripts if you copy some text from your Windows computer and paste it into your putty window.

Even if you're using an all Linux system, the copy and paste action can still get you.
For example, if you copy some text from a web page that has carriage returns and then paste that into a text file, that file may contain those carriage returns.