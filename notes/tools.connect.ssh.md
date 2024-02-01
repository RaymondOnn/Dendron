---
id: 5ce2ngpip8yoq73swd35rnt
title: ssh
desc: ''
updated: 1705296673573
created: 1704172551833
---
- https://medium.com/@ratnesh4209211786/simplified-ssh-server-setup-within-a-docker-container-77eedd87a320

- [Linux: Installing ssh vs openssh](https://askubuntu.com/questions/814723/whats-the-difference-between-ssh-and-openssh-packages)  
- https://www.youtube.com/watch?v=DYYxLSrJdW8

``` py
import re
import getpass
from fabric import Connection, Config

password = getpass.getpass("Enter your root password: ")

config = Config(overrides={'sudo': {'password': password}})
conn = Connection(<IP_ADDRESS>, user = <USER>, config=config)

result = conn.run("ls -la", hide=True)
print(result.stdout)

with conn.cd("/mnt/c/Users/flori/Desktop"):
    conn.run("touch myfile.txt")
    conn.run("pwd")

# run with root privileges
conn.sudo("apt install vim")

# automate extracting ip address
result = conn.run("ifconfig")
lines = result.stdout.split('\n')
inet_lines = [l for l in lines if "inet " in l and "127.0.0.1" not in l]
span = re.search("inet ([0-9]+\.){3}[0-9]+", inet_lines[0]).span()
ip_address = inet_lines[0][span[0]+5:span[1]]
print(ip_address)
```