---
id: hba08e2flrm6n8y31riuh70
title: bash
desc: ''
updated: 1697018589445
created: 1694732979398
---

- [Math Functions](https://www.youtube.com/watch?v=YDnp2oEiF64&list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w&index=4&ab_channel=LearnLinuxTV)
- <https://www.youtube.com/playlist?list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w>

## What is BASH?

- A shell language
- **B**ourne **A**gain **SH**ell
- Easy commands

## Why BASH?

``` diff
+ Most used shell
+ Comes with Linux, other OS
- No OOP
- Difficult syntax compared to Python
- Newer tools like Ansible
```

## Basic Commands

- `echo $SHELL`: Find out what is the default shell of the system
- `which bash`: Find out what command to use to start using bash

## Writing a bash script

###### `myscript.sh`
** Always start the script with a shebang "#!/bin/bash"!!!**

``` bash
#!/bin/bash       # start with a shebang to indicate which interpreter to use

# echo: similar concept to 'print' in python
echo "Hello World!"

echo "My current working directory is:"
pwd    # current working directory

```

###### Shell Commands

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

## Universal Update Script

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

## Storing Scripts
- FHS: https://wiki.linuxfoundation.org/lsb/fhs


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

## Data Streams

