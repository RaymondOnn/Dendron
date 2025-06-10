---
id: ngzsba3z39ujtgr2mczukme
title: Sftp
desc: ''
updated: 1745724779582
created: 1745693384298
---

SFTP (Secure File Transfer Protocol) is a secure way of transferring files over a network, using encryption to protect both the data and commands. It is commonly used to upload or download files to/from remote servers securely. Unlike FTP, SFTP encrypts the data, making it much safer for sensitive transfers.

### What is SFTP?

- SFTP stands for Secure File Transfer Protocol, and it is part of the SSH (Secure Shell) protocol suite.
- It provides secure file access, transfer, and management over a secure connection, typically using port 22 (the same port as SSH).
- Key differences from FTP:
  - Encryption: SFTP encrypts both data and commands, ensuring confidentiality.
  - Authentication: SFTP uses SSH keys or passwords for authentication.
  - Firewall-friendly: Unlike FTP (which uses multiple ports), SFTP uses a single port (22), making it easier to manage through firewalls.

### Setting Up an SFTP Server

- To set up an SFTP server, you typically need to have SSH installed and configured, as SFTP uses SSH for secure connections.
- Here’s how you can set it up:
  - On Linux/Unix (server-side):
    - Ensure the SSH service is installed and running:

        ```bash
        sudo apt install openssh-server
        sudo systemctl enable ssh
        sudo systemctl start ssh
        ```

    - By default, SFTP is enabled as part of the SSH server configuration.
    - You can configure SFTP-specific settings in `/etc/ssh/sshd_config`, such as:
      - Limit user access to SFTP only.
      - Restrict file upload/download directories.
    - Configure SSH for SFTP-only access: To restrict a user to SFTP access only, you can modify the sshd_config file:

        ```bash
        Subsystem sftp internal-sftp
        Match User sftpuser
        ChrootDirectory /home/sftpuser
        ForceCommand internal-sftp
        AllowTcpForwarding no
        ```

### Connecting to an SFTP Server

- To initiate a connection to an SFTP server, you use the sftp command.
- Here's the syntax:

    ```bash
    sftp username@hostname
    ```

- You can specify a port if the server uses a non-default port:

    ```bash
    sftp -P 2222 username@hostname
    ```

- Authentication: You'll either be prompted for a password or use an SSH key if the server is set up for key-based authentication.

### Common SFTP Commands

- Once connected to an SFTP server, you can use several commands to manage files. Here's a list of the most common SFTP commands:

- `ls` – List files in the current directory on the remote server.

    ```bash
    sftp> ls
    ```

- `cd` – Change directory on the remote server.

    ```bash
    sftp> cd /path/to/dir
    ```

- `lcd` – Change local directory on your computer.

    ```bash
    sftp> lcd /local/path
    ```

- `get` – Download a file from the remote server to your local machine.

    ```bash
    sftp> get remote_file.txt
    ```

- `put` – Upload a file from your local machine to the remote server.

    ```bash
    sftp> put local_file.txt
    ```

- `exit` or `quit` – Close the SFTP session.

    ```bash
    sftp> exit
    ```

- `mget` – Download multiple files at once.

    ```bash
    sftp> mget *.txt
    ```

- `mput` – Upload multiple files at once.

    ```bash
    sftp> mput *.txt
    ```

- `rm` – Remove a file on the remote server.

    ```bash
    sftp> rm remote_file.txt
    ```

- `mkdir` – Create a directory on the remote server.

    ```bash
    sftp> mkdir new_directory
    ```

- `rmdir` – Remove a directory on the remote server (only if empty).

    ```bash
    sftp> rmdir empty_directory
    ```

### SFTP Using SSH Keys

- For password-less login and better security, you can use SSH keys instead of passwords.
- Steps:
    1. Generate SSH Key Pair (on local machine):

        ```bash
        ssh-keygen -t rsa -b 4096
        ```

    2. Copy the Public Key to the Server:

        ```bash
        ssh-copy-id username@hostname
        ```

    3. Now, you can log in via SFTP without entering a password:

        ```bash
        sftp username@hostname
        ```

### Limitations of SFTP

- While SFTP is very secure, there are some limitations to be aware of:
  - Speed: SFTP is slower than FTP, especially with large files, due to encryption overhead.
  - No resume capability: Unlike FTP, SFTP doesn't support resuming transfers of partially uploaded files (unless using rsync with SSH).
  - Limited protocol options: SFTP is focused on file transfer and lacks some of the features of FTP, such as passive/active modes.

### Using SFTP in Scripts

- You can automate SFTP tasks with shell scripts.
- For example, to upload a file using a script:
    ```bash
    # !/bin/bash
    sftp username@hostname <<EOF
    put local_file.txt /remote/path/
    exit
    EOF
    ```

This script logs in to the remote SFTP server, uploads a file, and then exits.

### Troubleshooting Common Issues

- Permission Denied: Ensure you have correct file permissions and ownership on the server.
- Connection Refused: Check if the SSH service is running and if the firewall allows connections on port 22.
- Timeouts: Verify that you have internet connectivity and that the server isn't blocking connections.
- Authentication Errors: Make sure the correct key or password is being used.

### Best Practices for Secure SFTP Usage

- Use strong SSH keys and disable password authentication.
- Restrict user permissions with proper file ownership and access rights.
- Monitor logs to ensure there are no unauthorized access attempts (/var/log/auth.log).
- Limit user access to specific directories using the ChrootDirectory directive in the sshd_config.
