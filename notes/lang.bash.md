---
id: hba08e2flrm6n8y31riuh70
title: bash
desc: ''
updated: 1698985767464
created: 1694732979398
---

- [Math Functions](https://www.youtube.com/watch?v=YDnp2oEiF64&list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w&index=4&ab_channel=LearnLinuxTV)
- <https://www.youtube.com/playlist?list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w>

### What is BASH?

- A shell language
- **B**ourne **A**gain **SH**ell
- Easy commands

### Why BASH?

``` diff
+ Most used shell
+ Comes with Linux, other OS
- No OOP
- Difficult syntax compared to Python
- Newer tools like Ansible
```

### Basic Commands

- `echo $SHELL`: Find out what is the default shell of the system
- `which bash`: Find out what command to use to start using bash

## Data Streams

- Allows us to control where normal output and errors are printed and user input is accepted
- types of data streams
  - std output: output printed to the screen that is not error (exit code 0)
  - std err

``` bash
find /etc -type f 

# Anything moved/copied into /dev/null is deleted
# "2>" is instructed to redirect anything of exit code 2 to /dev/null
# Hence all errors are hidden
find /etc -type f 2> /dev/null

```

## Debugging

## Why Debug
- A bug is really an error
- Examine the inner workings of your script
- Determine the root of unexpected behaviour
- Fix bugs


A software bug is an error in a computer program that causes it to produce an unexpected or incorrect result.
Most of the bugs are actually mistakes in the program's code or in its design.
If you encounter a bug or an error in one of your scripts, you'll want to see exactly what is happening during the execution of that script.
Maybe something isn't working as you initially anticipated and you want to figure out where things are going wrong and how you can update your script so that it performs as expected.
Sometimes your script will produce an incorrect result or behave in unintended ways.
Sometimes it will simply stop because of a syntax error or typo.
This process of finding errors in your script or fixing unexpected behaviors is called debugging.

The bash shell provides some options that can help you in debugging your scripts.
You can use these options by updating the first line in your script to include one or more of these options.
The most popular of these options is the '-x' option.
The '-x' option prints commands and their arguments as they are executed.
This means that, instead of variables being displayed, the values of those variables are displayed.
The same thing goes for expansions.

Wildcards aren't displayed, but what they expand to is displayed.
You'll sometimes hear this type of debugging called "print debugging," tracing, or an x-trace.
If you are using this option in a shell script, simply add -x to the end of the shebang line.
If you want to do this at the command line, run 'set -x'.
Use 'set +x' to stop this debugging behavior.
You can also use this option for just a portion of your script.
Just before you want to start displaying the commands to the screen, add a 'set -x line'.
Place 'set +x' on a line after the section of the shell script that you're debugging.
Again set -x will start the x-trace while set +x will stop the x-trace.
Here is a very simple example that demonstrates the use of the -x option.
You can see that -x has been added to the end of the shebang.
At bottom of the screen is the output you'll see when you run this script.
You'll notice that there are lines that start with a plus sign.
Those are the commands that are being executed from the script.

In this example, there are two commands that are executed.
The first is setting the value of the TEST_VAR variable.
The second command is to echo that value to the screen.
In the output, the result of the echo command, which is "test," is displayed.
There is no plus sign in front of it because it is output as a result of a command and not a command itself.
Here is another example.
This shows how you can turn debugging on for just a portion of your script.
First, the TEST_VAR variable is set.
You'll see in the output that nothing is listed for this command because debugging isn't on at this point.
The next line turns on debugging with the set -x command.
Again, nothing is displayed in the output for this action.
Now the TEST_VAR is echoed to the screen.
The echo command is displayed because debugging is now on.
Of course, the output of the echo command is displayed next on the screen.
We use set +x to turn off debugging.
That set command is displayed on the screen.
Finally, the hostname command is executed and only its output is displayed.
Again, this is to demonstrate how you can encapsulate a block of code with set -x and set +x to debug that particular section of code.
You can do the exact same thing with the other options that we'll be covering next.
Another useful option that can help you in finding errors in your scripts is the '-e' option.
It causes your script to exit immediately if a command exits with a non-zero exit status.
Remember that an exit status of zero indicates the successful completion of a command and any exit status other than zero indicates some sort of error.
This can really help you pin-point exactly where the problem is.
You can use this in combination with other options including the -x option.
When used as an argument to the bash command, these options act like any other options for other commands.
Options that do not take arguments can be combined and only one hyphen is required followed by the options.
Also, it doesn't matter in which order they are used.
So we can use '-ex' or '-xe'.
If you want, you can use a hyphen before each option, but this is unconventional.
Here is an example using the -e option.
First, a value is assigned to the variable FILE_NAME.
Next the 'ls' command is executed using FILE_NAME as an argument.
Finally, the contents of the FILE_NAME variable are displayed to the screen.
However, when you execute this script, the ls command returns a non-zero exit status since the file doesn't exist.
Because the -e option was used, the execution of the program halts and the echo command is never attempted.
This time we'll use both the -e and -x options.
The -x option causes the commands to be displayed on the screen.
First, it displays the creation of the FILE_NAME variable.
Next, it displays the ls command.
You can clearly see what ls is doing in this example.
It's trying to display information about a file named, /not/here.
Of course, this causes an error and the script stops because of the -e option.
Yet another useful option that can help you in the debugging process is the '-v' option.
It prints the shell commands just like they are read in from the script.
'-v' prints everything before any substitutions and expansions are applied.
The -x option performs variable and wildcard expansion, but the -v option does not.
You can use them in combination to see what a line looks like before and after substitutions and expansions occur.
Here is an example of the -v option.
It causes every line of the script to be displayed to the screen before it is executed.
You'll notice that the lines are exactly as they are in the shell script.
In this script, the only thing that happens is that test gets echoed to the screen.
Here is what would happen if we added the -x option to the previous script.
What's useful about this output is that we can see the how a command looks in the shell script and how it actually gets executed.
Take the echo command, for example.
We can see in the script that we run 'echo $TEST_VAR' and that actually causes echo test to be executed.
From the command line, using a bash shell, you can run help set.
This will display information about the options we covered in this lesson plus other options that are available.
You might want to pipe this output to a pager like less so you can easily scroll through all the options and their descriptions.
In this lesson, you learned how to use a few different built-in bash options to aid you in debugging shell scripts.
You learned about the -x option, which prints commands as they will be executed.
You learned how to execute a script to fit in counters and error by using the '-e' option.
Finally, you learned how to display the commands in your shell script exactly as they appear by using the -v option.