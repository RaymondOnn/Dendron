---
id: 1lwonsiz757ctcsprt6ctc1
title: Umask
desc: ''
updated: 1745725780001
created: 1745725011058
---

### What is `umask`?

The umask (user file creation mask) is a Linux/Unix command and system setting that controls the default permissions for files and directories when they are created. It acts as a mask that removes permissions from the default settings for files and directories. In other words, it determines the permissions that will not be given to a new file or directory.

### How umask Works

When a new file or directory is created, it typically has default permissions. The default permissions for a file are generally 666 (read and write for everyone), and for a directory, they are 777 (read, write, and execute for everyone). The umask value then "masks" these defaults by subtracting the umask value from the default permissions.

- For example:
  - Default for files: 666 (rw-rw-rw-)
  - Default for directories: 777 (rwxrwxrwx)

- The umask value is subtracted from these default permissions, which results in the final permissions that are assigned to newly created files or directories.

### Understanding the Umask Value

- A umask value is a 3-digit octal number, where each digit represents the permissions for user, group, and others.
- Here's the breakdown:

    | Permission | Octal Value |
    |---|---|
    | Read | 4 |
    | Write | 2 |
    | Execute | 1 |

- The umask value subtracts these numbers from the default permissions (666 for files, 777 for directories).
  - For files, the default permissions are 666, but the umask will subtract permissions (i.e., remove permissions).
  - For directories, the default permissions are 777, and again, the umask will subtract permissions.

### How Does Umask Work in Practice?
Let's walk through a few examples with different umask values:

- Example 1: Default Umask Value (022)
  - If your umask is set to 022, this means:
    - User (owner) gets read and write permissions.
    - Group gets read permission.
    - Others get read permission.
  - Let's calculate what happens when a file or directory is created:
    1. File Creation (default 666):
       - umask = 022
       - Default permissions = 666 (rw-rw-rw-)
       - Final permissions = 666 - 022 = 644 (rw-r--r--)
       - Result: The file will have read and write permissions for the user and read-only for group and others.

    2. Directory Creation (default 777):
       - umask = 022

Default permissions = 777 (rwxrwxrwx)

Final permissions = 777 - 022 = 755 (rwxr-xr-x)

Result: The directory will have read, write, and execute permissions for the user, and read and execute permissions for group and others.

Example 2: More Restrictive Umask Value (0777)
If your umask is set to 0777, it means:

User, group, and others will have no permissions (all permissions are masked out).

File Creation (default 666):

umask = 0777

Default permissions = 666 (rw-rw-rw-)

Final permissions = 666 - 0777 = 000 (no permissions)

Result: The file is created with no permissions. This means no one can read, write, or execute the file.

Directory Creation (default 777):

umask = 0777

Default permissions = 777 (rwxrwxrwx)

Final permissions = 777 - 0777 = 000 (no permissions)

Result: The directory would also be created with no permissions, meaning no one can access or modify the directory.

Example 3: More Open Umask Value (0022)
If your umask is set to 0022, this means:

User (owner) gets read and write permissions.

Group and others get read permission.

File Creation (default 666):

umask = 0022

Default permissions = 666 (rw-rw-rw-)

Final permissions = 666 - 0022 = 644 (rw-r--r--)

Result: The file will have read and write permissions for the user and read-only for group and others.

Directory Creation (default 777):

umask = 0022

Default permissions = 777 (rwxrwxrwx)

Final permissions = 777 - 0022 = 755 (rwxr-xr-x)

Result: The directory will have read, write, and execute permissions for the user, and read and execute permissions for group and others.

Checking and Setting the Umask Value
Check the current umask value:

bash
umask
This will display the current umask value, such as 0022.

Set the umask value temporarily: To change the umask for the current session (until you log out):

bash
umask 022
Set the umask value permanently: You can set the umask permanently by adding it to your shell configuration file (e.g., .bashrc, .bash_profile, or .zshrc):

bash
echo "umask 022" >> ~/.bashrc
source ~/.bashrc
Why Use Umask?
The umask is important because it helps enforce security policies on newly created files and directories. For instance:

You don’t want files to be world-writable by default.

You can make files or directories private by setting a more restrictive umask (e.g., 0777).

By adjusting the umask, you can control the default permissions for newly created files and directories, thus minimizing security risks.

Summary
umask is a system setting that defines the default permissions for newly created files and directories.

It works by subtracting permissions from the default file/directory permissions (666 for files and 777 for directories).

Common values:

022: Files get 644, directories get 755.

0777: No permissions on files and directories (dangerous).

0022: More open, files get 644, directories get 755.

The umask helps manage security by controlling default access, and it’s a key component of file system permissions.
