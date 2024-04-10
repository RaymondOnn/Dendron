---
id: 6o5gibcoh0a0rcf2k0w0qoh
title: provisioners
desc: ''
updated: 1711797327633
created: 1711791316513
---


### Provisioners and Null Resources

-   Terraform provisioners have nothing in common with providers.
-   Used to run different commands or scripts on your local machine or a remote machine, and also copy files from your local machine to a remote one.
-   Provisioners exist inside of a resource
    -   Hence, to use one, will need to add a provisioner block in that particular resource.
    -   You can run provisioners either when the resource is created (default) or run it when a resource is destroyed.
    -   A provisioner is not able to reference the parent resource by its name, but they can use the `self` object which actually represents that resource.
-   All provisioners support two interesting options `when` and `on_failure`.
    -   By default, `on_failure` is set to `fail`
        -   i.e. will fail the `apply` if the provisioner fails, which is expected Terraform behaviour
        -   can set it to ignore a fail by setting it to `continue`.
-   There are 3 main ypes of provisioners:
    -   local-exec
    -   file (used with a connection block)
    -   remote-exec (used with a connection block)
    -   Note that there are also some vendor-specific ones too
-   They are considered a last resort, as they are not a part of the Terraform declarative model.
-   While not recommended, they might be your only option.

#### Connection Blocks

-   Connection blocks, support both ssh and winrm, and allows for connecting to both Linux and Windows vms.
-   There is also the option to connect via a bastion host or a proxy

    ```sh

    connection {
        type        = "ssh"
        user        = "root"
        private_key = "private_key_contents"
        host        = "host"
    }
    ```

#### Null resource

-   A null resource is basically something that doesn’t create anything on its own, but you can use it to define provisioners blocks.
-   They also have a “trigger” attribute, which can be used to recreate the resource, hence to rerun the provisioner block if the trigger is hit.

    ```sh
    resource "null_resource" "null_resource_simple" {
        # trigger is optional
        triggers = {
            id = aws_instance.ec2_example.id   #<--- triggers if value changes
        }
        provisioner "local-exec" {
            command = "echo Hello World"
        }
    }
    ```

#### Local-Exec

-   a local-exec block will run a script on your local machine.
-   Apart from the `when` and `on_failure` options, there are a couple of other options you can specify:
    -   `command`: what to run (required)
    -   `working_dir`: where to run it
    -   `interpreter`: what interpreter to use (e.g /bin/bash). By default terraform will decide based on your system os
    -   `environment`: key/value pairs that represent the environment
-   You can use this to run different scripts before or after an `apply` of a specific resource by using `depends_on`.

    ```sh
    # Let’s see this in action in a null resource and observe the output of a terraform apply

    resource "null_resource" "this" {
        provisioner "local-exec" {
            command = "echo Hello World!"
        }
    }

    # null_resource.this: Creating...
    # null_resource.this: Provisioning with 'local-exec'...
    # null_resource.this (local-exec): Executing: ["/bin/sh" "-c" "echo Hello World!"]
    # null_resource.this (local-exec): Hello World!
    # null_resource.this: Creation complete after 0s [id=someid]
    ```

#### File

-   The file provisioner is used to copy a file from your local vm to a remote vm.
-   There are three arguments that are supported:
    -   `source`: what file to copy
    -   `content`: the direct content to copy on the destination
    -   `destination`: where to put the file
-   Will require a connection block to make sure it works properly.

    ```sh
    # An example on an ec2 instance.

    provider "aws" {
        region = "us-east-1"
    }

    locals {
        instance_type = "t2.micro"
        most_recent   = true
    }

    data "aws_ami" "ubuntu" {
        filter {
            name   = "name"
            values = ["ubuntu-*"]
        }
        most_recent = local.most_recent
    }

    resource "aws_key_pair" "this" {
        key_name   = "key"
        public_key = file("~/.ssh/id_rsa.pub")
    }

    resource "aws_instance" "web" {
        ami           = data.aws_ami.ubuntu.id
        instance_type = local.instance_type
        key_name      = aws_key_pair.this.key_name
    }

    resource "null_resource" "copy_file_on_vm" {
        depends_on = [aws_instance.web]
        connection {
            type        = "ssh"
            user        = "ubuntu"
            private_key = file("~/.ssh/id_rsa")
            host        = aws_instance.web.public_dns
        }
        provisioner "file" {
            source      = "./file.yaml"
            destination = "./file.yaml"
        }
    }

    # null_resource.copy_file_on_vm: Creating...
    # null_resource.copy_file_on_vm: Provisioning with 'file'...
    # null_resource.copy_file_on_vm: Creation complete after 2s [id=someid]
    ```

#### Remote-Exec

-   Remote-Exec is used to run a command or a script on a remote-vm.
-   It supports the following arguments:
    -   `inline`: list of commands that should run on the vm
    -   `script`: a script that runs on the vm
    -   `scripts`: multiple scripts to run on the vm
-   Only one of the above arguments is needed as they are not going to work together.
-   Similar to file, you will need to add a connection block.

    ```sh
    ...

    resource "null_resource" "remote_exec" {
        depends_on = [
            aws_instance.web
        ]
        connection {
            type        = "ssh"
            user        = "ubuntu"
            private_key = file("~/.ssh/id_rsa")
            host        = aws_instance.web.public_dns
        }
        provisioner "remote-exec" {
            inline = [
                "mkdir dir1"
            ]
        }
    }

    # null_resource.remote_exec: Creating...
    # null_resource.remote_exec: Provisioning with 'remote-exec'...
    # null_resource.remote_exec (remote-exec): Connecting to remote host via SSH...
    # null_resource.remote_exec (remote-exec):   Host: somehost
    # null_resource.remote_exec (remote-exec):   User: ubuntu
    # null_resource.remote_exec (remote-exec):   Password: false
    # null_resource.remote_exec (remote-exec):   Private key: true
    # null_resource.remote_exec (remote-exec):   Certificate: false
    # null_resource.remote_exec (remote-exec):   SSH Agent: true
    # null_resource.remote_exec (remote-exec):   Checking Host Key: false
    # null_resource.remote_exec (remote-exec):   Target Platform: unix
    # null_resource.remote_exec (remote-exec): Connected!
    # null_resource.remote_exec: Creation complete after 3s [id=someid]
    ```

> NOTE: You have to make sure that you can connect to your vm, so make sure you have a security rule that permits ssh access in your security group.