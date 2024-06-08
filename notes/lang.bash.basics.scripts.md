---
id: 9cov4wsktpr1ab92etvoju5
title: scripts
desc: ''
updated: 1714446214913
created: 1698720590622
---

## Scripts

### Writing a bash script

-   A script is a command line program that contains a series of commands.
-   Commands contained in the script are executed by an interpreter.
-   In the case of shell scripts, the shell acts as the interpreter and executes the commands listed in the script one after the other.
-   Anything you can execute at the command line you can put into a shell script.
    - Shell scripts are great at automating tasks.

    **`myscript.sh`**

    ```bash
    #!/bin/bash       # start with a shebang to indicate which interpreter to use

    # echo: similar concept to 'print' in python
    echo "Hello World!"

    echo "My current working directory is:"
    pwd    # current working directory
    ```
> **Always start the script with a shebang "#!/bin/bash"!!!**

<br>

> [**Shebang Interpreter Directive**](https://linuxize.com/post/bash-shebang/)
> The Shebang interpreter directive takes the following form: `#!interpreter [arguments]`
> - The directive must be the first line in the script.
> - The directive must start with shebang #!
> - White space after the shebang characters is optional.
> - Interpreter is the full path to a binary file (ex: /bin/sh, /bin/bash).
> - Interpreter arguments are optional.
> - Examples:
>   ```
>   #!/bin/bash - Uses bash to parse the file.
>   #!/usr/bin/env perl - Uses the env command to find the path to the perl executable.
>   #!/usr/bin/python Executes the file using the python binary.
>   ```
<br>

### Running a script

-   If the script is in your path, you can simply type in the script name and it will execute.
-   If it's not in your path, then you'll need to give it a relative or full path to your script.

```bash
# edit / create script
# nano is the name of the text editor
nano myscript.sh

sudo chmod +x myscript.sh    # sudo: root privilege, chmod: change file permissions

# to run the script. NOTE: .sh is optional for shell scripts
./myscript.sh
>>> Hello World!
>>> My current working directory is:
>>> /home/jay
```

### Shell Script Order and Checklist

1. Shebang
2. Comments / file header i.e. author, version of script
3. Global Variables
4. Functions
    1. Use Local Variables
5. Main Script Contents
6. Exit with an exit code (i.e. exit <STATUS> at various exit points)

### Universal Update Script

**`./update.sh`**

```bash
#!/bin/bash

release_file=/etc/os-release

if grep -q "Arch" $release_file
then
    # The host is based on Arch, run the pacman update command
    sudo pacman -Syu
fi

# ||: OR operator, &&: AND operator
if grep -q "Debian" $release_file || grep -q "Ubuntu" $release_file
then
    # The host is based on Debian or Ubuntu
    # Run the apt version of the commanf
    sudo apt update
    sudo apt dist-up-grade
fi
```

### Storing Scripts

-   FHS: <https://wiki.linuxfoundation.org/lsb/fhs>

```bash
# move to /usr/local/bin
# In linux, file extension not required
sudo mv update.sh /usr/local/bin/update

# assign root privileges so that root permission needed to edit file
sudo chown root:root /usr/local/bin/update

which update
>>> /usr/local/bin/update

# PATH contains all directories that the shell will scan for a script to run
# Note variables in CAPS are system variables
echo $PATH
>>> /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:...

# since /usr/local/bin/ is in the PATH variable, we can run the script directly
update

# adding directory into $PATH
export PATH=/usr/local/bin:$PATH
```

### The `source` Command

-   The source command reads and executes the content of a file.
-   If the sourced file is a bash script, the overall effect comes down to running it.

#### Bash Variables and Definitions of Functions

##### When script is executed using the source command

-   The script will run in the same shell where we source it
-   Hence, the script accesses all variables from the shell where the source command is issued.
-   This communication works in the opposite direction – all definitions from the sourced file become available in the parent shell.

##### When script is executed via the bash command or just by typing its name

-   a new shell instance is created.
-   Therefore, the script can access only variables and functions defined with keyword export in the parent shell.
-   In addition, all definitions of the descendant shell disappear when it exits.

```bash
# test_var.sh

#!/bin/bash
echo "expVar = $expVar"
echo "myVar = $myVar"

# Let’s set the variables in the terminal then run the script in both ways to compare results:

export expVar = "Exported variable"
myVar="My variable"

./test_var
expVar = Exported variable
myVar =

source test_var
expVar = Exported
variable myVar = My variable
```

-   This characteristic makes the source command extremely useful to share content and functionality between different files.

##### Other Significant Properties

In addition, the `source` command’s features include:

-   it searches for the script in folders listed in the PATH variable of the current shell
-   it recognizes an absolute or relative path given to the script
-   the script does not need to be executable
-   it returns the status of the last executed command in the script
-   if sourced script issues the exit command, the sourcing one exits, too

#### Passing Environment Variables From a File

-   Using the `source` command, we can collect variables in the file and import them into the script.
-   The well-known case is reading and setting variables from the user’s .bash_profile (or .profile).
-   This is especially useful in the case of cron jobs because the shell of cron usually has a very sparse set of environment variables.

Let’s assume that we want to pass all environment variables of the user’s interactive shell to the cron job script. So, let’s begin the script by sourcing profile files:

```bash
#!/bin/bash
source /etc/profile
source ~/.bash_profile
source ~/.bashrc

# do something useful

```

#### Building a Library of Functions

-   Similarly, the source command allows for importing functions that were defined and saved in separate file

```bash
# Suppose our lib_example library contains two functions:
# - my_name: finds the name of the current user (with the whoami command)
# - my_used_disk_space:
#       - calculates the disk space occupied by the user’s home directory (uses the du command).
#       - cut removes the folder name from the output of du.

# ------------------ define the functions in file lib_example --------------------------
#!/bin/bash
function my_name()
{
whoami
}

function my_used_disk_space()
{
du -sh ~ | cut -f1
}


# Accessing these functions from another script, lib_test, using the source command:

#!/bin/bash
source lib_example
echo "My name is $(my_name), I am using $(my_used_disk_space)"
Let’s start the script and check the result:

>>> ./lib_test
>>> My name is joe, I am using 46G
```

#### Setting a Correct Path

-   The source command assumes the current working directory when trying to find scripts
-   Hence, this is prone to errors as things will only work correctly if
    -   the main script was invoked from the folder where it is located
    -   the source script would need to be in the same directory
-   Calling from another directory will lead to errors

1. Providing an Absolute Path

    - We can precede the name of the sourced script with its full path:
    - It’s a rock-solid solution, although it demands rewriting the script when we change its location.

    ```bash
    source /home/joe/example/lib_example
    ```

2. Using the dirname Command
   - Since both scripts are in the same folder, we can use `dirname` to extract the path from the name of the wrapper script.

    ``` bash
    #  $0 refers to the full name of the sourcing script.
    source $(dirname "$0")/lib_example
    ```

1. Make Use of the PATH Environment Variable
   - Since source searches for files in folders listed in PATH, we can move the sourced script to one of the folders listed in PATH i.e. /home/joe/.local/bin.

    ``` bash
    # Examining the PATH of user joe:
    /home/joe/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/joe/.local/bin:/home/joe/.local/bin
    ```

### Passing Arguments With the source Command
- the source command allows passing arguments to the script. 
- However, in the case of sourcing from another script, `source` passes the arguments of the wrapping script to the sourced one, **unless** the latter’s arguments are explicitly given.


``` bash
# script arg_list

#!/bin/bash
echo "My argument(s): $@"

# Using arg_list inside another script, arg_test:

#!/bin/bash
source arg_list foo_bar

# Invoke arg_test with two arguments:

./arg_test foo bar
My argument(s): foo_bar

# NOTE: source passes the argument foo_bar to the included script 
#       independently of the arguments foo and bar of the wrapping script.

# Changing arg_test to skip the argument of the arg_list script:

#!/bin/bash
source arg_list

# One more time, let’s examine the result:

>>>./arg_test foo bar
>>> My argument(s): foo bar

```

