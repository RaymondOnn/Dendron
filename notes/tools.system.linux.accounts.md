---
id: yoinvk38k65pnxw3i62kn23
title: Accounts
desc: ''
updated: 1745729294256
created: 1745726494178
---

In Linux, managing user accounts and their related properties is a critical aspect of system administration. Proper account management ensures that the system remains secure, and that users can access necessary resources within their permissions. Understanding commands like chage, usermod, useradd, passwd, and others will help you configure and maintain user accounts effectively.

### User Account Basics

- A user account in Linux consists of several key components:
  - Username: A unique identifier for each user.
  - Password: A secure password for authenticating the user.
  - UID (User ID): A numeric identifier associated with the user.
  - GID (Group ID): The primary group identifier for the user.
  - Home Directory: The user's personal directory, typically /home/username.
  - Shell: The default shell assigned to the user (e.g., /bin/bash).

### Key Commands for User and Group Management

- Here are some essential commands for user and group management:

#### `useradd` – Create a new user

```bash
sudo useradd -m -s /bin/bash username
```

- `-m`: Create the user’s home directory if it doesn’t exist.
- `-s`: Specify the shell for the user (default is /bin/bash).

#### usermod – Modify an existing user account

```bash
sudo usermod -aG groupname username
```

- `-aG`: Add the user to a specific group (without removing the user from other groups).

#### userdel – Delete a user account

```bash
sudo userdel -r username
```

- `-r`: Remove the user’s home directory and mail spool.

#### groupadd – Create a new group

```bash
sudo groupadd groupname
```

#### groupdel – Delete a group

```bash
sudo groupdel groupname
```

#### passwd – Set or change a user’s password

```bash
sudo passwd username
```

#### chown - Change the ownership of files or directories

```bash
sudo chown username:groupname /path/to/file
```

#### chgrp – Change the group ownership of files or directories

```bash
sudo chgrp groupname /path/to/file
```

### Password Management and Policies

- In Linux, users authenticate via their passwords, and administrators can configure password policies to enforce strong security.
- Password expiration: You can configure user passwords to expire after a certain period of time to force the user to change it.
- Password aging: The system can enforce a minimum age for passwords (i.e., how often they must be changed).

### The `chage` Command: Managing Password Expiry

- The `chage` command is used to change user password expiration settings, including when the password expires, how frequently it must be changed, and how long the account remains active after the password expires.

- Basic Syntax:

    ```bash
    chage [options] username
    ```

- Key Options:
  - `-l`: List the####  password aging information for a user.

    ```bash
    chage -l username
    ```

  - `-M`: Set the maximum number of days between password changes.

    ```bash
    sudo chage -M 90 username
    # This command sets the maximum password age to 90 days. 
    # After this period, the user will be required to change their password.
    ```

  - `-m`: Set the minimum number of days between password changes

    ```bash
    sudo chage -m 7 username
    # This forces the user to wait at least 7 days before changing their password again.
    ```

  - `-W`: Set the number of days before the password expires to start warning the user

    ```bash
    sudo chage -W 14 username
    # This command sets a warning period of 14 days before the password expires.
    ```

  - `-I`: Set the number of days of inactivity allowed after the password expires before the account is locked

    ```bash
    sudo chage -I 30 username
    If a user doesn’t log in within 30 days of their password expiring, their account will be locked.
    ```

  - `-E`: Set the account expiration date

    ```bash
    sudo chage -E 2025-12-31 username
    # This command sets the account to expire on December 31, 2025.
    ```

  - `-d`: Set the date of the last password change

    ```bash
    sudo chage -d 2023-01-01 username
    # This command sets the date when the user last changed their password. 
    # This can be useful for auditing or troubleshooting.
    ```

### Account Expiration and Locking

- Locking an Account: To disable a user account temporarily, you can lock it with the following command:

    ```bash
    sudo usermod -L username
    ```

  - This places an exclamation mark (!) in front of the user's password hash, effectively disabling login for that user.

- Unlocking an Account: To enable a user account again

    ```bash
    sudo usermod -U username
    ```

- Setting Account Expiry: You can set an account to expire on a specific date using usermod:

    ```bash
    sudo usermod -e 2025-12-31 username
    ```

  - This will disable the user account on December 31, 2025.

### Managing User Groups

- In Linux, groups are used to organize users and manage permissions collectively.
- Groups help in controlling access to files and resources. Some important group management commands are:

    - `groupadd`: Create a new group

        ```bash
        sudo groupadd groupname
        ```

    - `usermod -aG`: Add an existing user to an additional group.

        ```bash
        sudo usermod -aG groupname username
        ```

    - `groups`: Show which groups a user is a part of.

        ```bash
        groups username
        ```

    - `gpasswd`: Administer group password for /etc/group.

        ```bash
        sudo gpasswd groupname
        ```

    - `groupdel`: Delete a group.

        ```bash
        sudo groupdel groupname
        ```

### Viewing Account Information
You can view various details about users and groups using these commands:

- `id`: Show user ID (UID), group ID (GID), and group membership

    ```bash
    id username
    ```

- `finger`: Display information about a user (if installed).

    ```bash
    finger username
    ```

- `getent passwd username`: Retrieve detailed account information from the system's database.

    ```bash
    getent passwd username
    ```

- `last`: S#### how the last logins of users.

    ```bash
    last username
    ```

- `lastlog`: Display the last login times for all users.

    ```bash
    lastlog
    ```

### Auditing and Security
- For system security and auditing purposes, it's important to regularly check account-related activities:
  - `auditd`: The Linux audit daemon can track user account changes, logins, and other security-related activities.
  - `/var/log/auth.log`: This file logs user authentication attempts (successful and failed).

- Regularly checking logs and account activities helps identify any suspicious activity and ensures compliance with security policies.

Practice Scenarios to Try:
Create a new user account and group.

Set a password expiration policy (e.g., force password change every 60 days).

Lock a user account temporarily and later unlock it.

Modify a user’s group memberships.

Set up an account expiration date.

Review user login history using last or lastlog.

Summary
Linux account management is about creating, modifying, securing, and deleting user accounts.

Key commands include useradd, usermod, passwd, groupadd, and chage.

chage is used to manage password expiration, aging, and account expiry settings.

Ensuring proper account expiration and password policies is critical for maintaining security.

Groups allow for better management of permissions, and logs help with auditing user activity.
