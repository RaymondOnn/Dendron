---
id: 1tb59tpmutbbufqjzrti8a1
title: Permissions
desc: ''
updated: 1745736715363
created: 1745691496648
---

Linux permissions are essential for controlling who can read, write, or execute files and directories on a system. Understanding permissions is crucial for system security and user management.

## The Basics of File Permissions

- Every file and directory in Linux has three types of permissions for three categories of users:

### User Categories

- User (u) – the file owner
- Group (g) – users in the file’s group
- Others (o) – everyone else

### Permission Types

- Files
  - Read (r) – view contents (4)
  - Write (w) – modify contents (2)
  - Execute (x) – run the file if it's a script or binary (1)
- Directories
  - Read = list contents
  - Write = create or delete files
  - Execute = access (enter) the directory
  - Without execute permission, even if you can read the directory, you can't cd into it.

#### Interpreting Notation

``` sh
- rwx r-x r-- user group
```

- first dash indicates a file. A directory would have a 'd' instead
- `rwx`: User hash read, write and execute permissions
- `r-x`: Group has read and execute permissions
- `r--`: Others have read permissions only

### Understanding Permission Notation

- You’ll see permissions in two forms:
  - Symbolic notation: rwxr-xr--
  - Octal notation: 754
    - 7 = 4+2+1 = read + write + execute
    - 5 = 4+0+1 = read + execute
    - 4 = 4+0+0 = read

### Changing Permissions

- `chmod`: change file permissions

    ```bash
    chmod 755 filename  # octal
    chmod u+x filename  # symbolic
    ```

- `chown`: change file ownership

    ```bash
    chown user:group filename
    ```

- `chgrp`: change the group

    ```bash
    chgrp groupname filename
    ```

### Access Control Lists (ACLs)

- For more granular control beyond the basic user/group/others model.
- Use `getfacl` and `setfacl`

    ``` bash
    setfacl -m u:john:r-- filename
    getfacl filename
    ```

### What Does World-Writable Mean?

- In Linux and Unix systems, file and directory permissions are split into three categories: user (owner), group, and others (also known as "world").
- When a file or directory is world-writable, it means any user (including those who are not the owner or part of the group) has the ability to modify (write to) that file or directory.
- This is achieved when the "others" category has write permission.

#### How Does This Look in Permissions?

- A file or directory with world-writable permissions will look like this when you run ls -l:

  ```bash
  -rwxrwxrwx  1 user  group  1234 Apr 27 12:34 example.txt
  ```

  - Here’s what each part means:
    - The `rwx` for user means the owner has read, write, and execute permissions.
    - The `rwx` for group means group members have read, write, and execute permissions.
    - The final `rwx` for others means anyone else (world) also has read, write, and execute permissions.

#### Why is World-Writable Dangerous?

- A world-writable file or directory is considered a security risk for several reasons:
  - Data Integrity Risk: Any user can modify or delete files. For example, if a log file is world-writable, any user could tamper with or erase logs, making it hard to track important actions or identify security breaches.
  - Privilege Escalation: If a user can modify certain files that other services or users depend on (e.g., configuration files, executables), it could lead to unauthorized access or privilege escalation.
  - Malicious Use: Malicious users could upload files or scripts that can harm the system (e.g., viruses, malware).

Example: World-Writable Directory
A world-writable directory might look like this:

bash
drwxrwxrwx  2 user  group  4096 Apr 27 12:34 shared_folder
This directory allows any user to create, delete, or modify files inside it.
