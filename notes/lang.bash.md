---
id: hba08e2flrm6n8y31riuh70
title: bash
desc: ''
updated: 1694739914627
created: 1694732979398
---

## What is BASH?

- A shell language
- Bourne Again SHell
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

- [Math Functions](https://www.youtube.com/watch?v=YDnp2oEiF64&list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w&index=4&ab_channel=LearnLinuxTV)

``` bash

# See file contents
cat file.txt


ls    # List Storage
pwd   
```

https://www.youtube.com/playlist?list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w

