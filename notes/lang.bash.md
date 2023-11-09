---
id: hba08e2flrm6n8y31riuh70
title: bash
desc: ''
updated: 1699252950938
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

