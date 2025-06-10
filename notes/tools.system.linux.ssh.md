---
id: bukaparc7rmwn574gbwablp
title: Ssh
desc: ''
updated: 1745736603601
created: 1705286499350
---

What is SSH?
SSH (Secure Shell) is a cryptographic network protocol used for secure communication over a potentially insecure network. It provides a secure channel for remote login, file transfers, and the execution of commands on remote systems. SSH is commonly used by system administrators and users to access remote Linux or Unix-based servers securely.

Unlike older protocols like Telnet and rlogin, SSH encrypts all data sent over the network, ensuring confidentiality and integrity, which is critical for preventing eavesdropping, man-in-the-middle attacks, and session hijacking.

How SSH Works
SSH operates in a client-server model, where:

SSH Client: This is the software (usually running on your local machine) that connects to the SSH server on a remote machine.

SSH Server: This is the service running on the remote machine that listens for incoming SSH connection requests.

Key Components of SSH
Authentication: SSH uses two main types of authentication:

Password Authentication: The most basic form of authentication where the user provides a password.

Public Key Authentication: A more secure method, where a private key is stored locally on the client machine, and a corresponding public key is placed on the server. If the public key matches the private key, authentication is successful.

Encryption: All data (commands, files, etc.) transmitted between the client and server is encrypted. SSH uses cryptographic algorithms like AES (Advanced Encryption Standard) for encryption, ensuring that the data remains confidential.

Integrity: SSH provides integrity through message authentication codes (MACs). This ensures that the data has not been tampered with during transmission.

Port Forwarding: SSH allows for port forwarding (tunneling), which can be used to forward ports securely over the SSH connection. This is particularly useful for accessing services (e.g., databases or web servers) securely over an encrypted connection.

How to Set Up SSH on Linux
Setting up SSH typically involves installing and configuring an SSH server (sshd), as well as configuring client software to connect to remote systems. Here's a step-by-step guide on setting up SSH on a Linux system.

1. Install SSH Server (sshd)
Most Linux distributions come with SSH installed by default. However, if it’s not installed, you can easily install it.

On Debian/Ubuntu-based systems:
bash
Copy
Edit
sudo apt update
sudo apt install openssh-server
On Red Hat/CentOS-based systems:
bash
Copy
Edit
sudo yum install openssh-server
On Fedora:
bash
Copy
Edit
sudo dnf install openssh-server
After installation, start and enable the SSH service:

bash
Copy
Edit
sudo systemctl start sshd        # Start the SSH server
sudo systemctl enable sshd       # Enable SSH to start on boot
Verify that SSH is running:

bash
Copy
Edit
sudo systemctl status sshd
This should output information showing that the sshd service is active and running.

2. Configure SSH Server (sshd_config)
The SSH server configuration file is located at /etc/ssh/sshd_config. You can customize SSH settings like enabling/disabling password authentication, configuring allowed IP addresses, and enabling public key authentication.

To modify this configuration, open the sshd_config file in a text editor:

bash
Copy
Edit
sudo nano /etc/ssh/sshd_config
Here are some key configuration options you may want to modify:

Port: The default port for SSH is 22. You can change it for added security (though it's not necessary).

bash
Copy
Edit
Port 22
PermitRootLogin: You can disable root login for better security.

bash
Copy
Edit
PermitRootLogin no
PasswordAuthentication: If you want to disable password-based authentication and rely solely on key-based authentication, set this to no.

bash
Copy
Edit
PasswordAuthentication yes    # Set to 'no' to disable password-based login
PubkeyAuthentication: Ensures that public key authentication is enabled.

bash
Copy
Edit
PubkeyAuthentication yes
AllowUsers: Restrict SSH access to specific users.

bash
Copy
Edit
AllowUsers user1 user2
After modifying the configuration, save the file and restart the SSH service to apply the changes:

bash
Copy
Edit
sudo systemctl restart sshd
3. Set Up SSH Client
Installing SSH Client (on the local machine)
Linux systems usually come with the SSH client (ssh) pre-installed. To check if it's installed, run:

bash
Copy
Edit
ssh -V
If it’s not installed, you can install it:

Debian/Ubuntu-based systems:

bash
Copy
Edit
sudo apt install openssh-client
Red Hat/CentOS-based systems:

bash
Copy
Edit
sudo yum install openssh-clients
Using SSH Client
To connect to a remote machine using SSH, you can use the following syntax:

bash
Copy
Edit
ssh username@hostname_or_ip_address
For example:

bash
Copy
Edit
ssh user@192.168.1.100
When you first connect, you'll be asked to confirm the authenticity of the host. Type yes to continue.

If you're using password authentication, you'll be prompted to enter the password.

If you're using public key authentication, no password will be needed if everything is set up correctly.

4. Setting Up SSH Key-Based Authentication
Key-based authentication is more secure than using passwords. It involves generating a pair of cryptographic keys: a private key (stored on your local machine) and a public key (stored on the remote server).

Generate an SSH Key Pair
On your local machine, generate an SSH key pair using the following command:

bash
Copy
Edit
ssh-keygen -t rsa -b 4096
This will generate a public/private RSA key pair in the ~/.ssh directory by default. You'll be prompted to enter a passphrase for added security (optional).

After the keys are generated, you’ll see two files:

id_rsa: The private key (keep it safe and never share it).

id_rsa.pub: The public key (this is the key you will copy to the server).

Copy the Public Key to the Remote Server
To copy your public key to the remote server, use the ssh-copy-id command:

bash
Copy
Edit
ssh-copy-id user@hostname_or_ip
This will append your public key to the ~/.ssh/authorized_keys file on the remote server.

Alternatively, you can manually copy the contents of id_rsa.pub to the remote server’s ~/.ssh/authorized_keys file.

Testing Key-Based Authentication
Once the public key is copied, try to connect again using SSH. You should be able to log in without a password (unless you set a passphrase on your key).

bash
Copy
Edit
ssh user@hostname_or_ip
5. Secure SSH Practices
To ensure secure SSH usage, follow these practices:

Disable Root Login: As mentioned, prevent root login over SSH. This can be done by setting PermitRootLogin no in the sshd_config file.

Use Key-Based Authentication: Use public-key authentication instead of passwords for added security. This reduces the risk of brute-force attacks.

Change the Default SSH Port: Changing the default SSH port (22) to something less obvious can reduce automated attack attempts. For example:

bash
Copy
Edit
Port 2222
Limit SSH Access by IP: You can configure firewalls to allow SSH only from specific IP addresses to reduce exposure to unauthorized access.

Use Fail2Ban: Fail2Ban is a tool that monitors SSH login attempts and blocks IPs that show suspicious activity (e.g., too many failed login attempts). It’s highly recommended for securing SSH.

Set Up Two-Factor Authentication (2FA): You can set up two-factor authentication for SSH using tools like Google Authenticator for an added layer of security.

6. SSH File Transfer
SSH can also be used for secure file transfers. You can use the following tools:

SCP (Secure Copy): To copy files over SSH:

bash
Copy
Edit
scp local_file user@hostname:/remote/directory
SFTP (Secure FTP): To transfer files interactively:

bash
Copy
Edit
sftp user@hostname
