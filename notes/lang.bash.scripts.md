---
id: 9cov4wsktpr1ab92etvoju5
title: scripts
desc: ''
updated: 1698720620558
created: 1698720590622
---


## Scripts
### Writing a bash script

- A script is a command line program that contains a series of commands.
- Commands contained in the script are executed by an interpreter.
- In the case of shell scripts, the shell acts as the interpreter and executes the commands listed in the script one after the other.
- Anything you can execute at the command line you can put into a shell script.
0 Shell scripts are great at automating tasks.

 **`myscript.sh`**

>**Always start the script with a shebang "#!/bin/bash"!!!**

``` bash
#!/bin/bash       # start with a shebang to indicate which interpreter to use

# echo: similar concept to 'print' in python
echo "Hello World!"

echo "My current working directory is:"
pwd    # current working directory

```

### Running a script

- If the script is in your path, you can simply type in the script name and it will execute.
- If it's not in your path, then you'll need to give it a relative or full path to your script.

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

``` bash
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

- FHS: <https://wiki.linuxfoundation.org/lsb/fhs>

``` bash
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