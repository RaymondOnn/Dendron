---
id: 1gqxivxjn9su38epd3johay
title: terraform
desc: ''
updated: 1712196759268
created: 1711702949703
---

-   CLI Commands: https://spacelift.io/blog/terraform-commands-cheat-sheet

### What is Terraform

-   a tool for building, changing and versioning infrastructre safely and efficiently
-   Developed by Hashicorp to make creating and takeing care of infrastructure resources easier.
-   Terraform uses HCL (Hashicorp Configuration Language) code to define its resources
-   Platform-agnostic
-   Terraform is stateful and keeps its state in a state file.
    -   Uses it and your configuration, to determine the actions needed to reach the desired state of the resources.
    -   ensures unnecessary resources is not created and helps to prevent errors and conflicts.
    -   However, this adds complexity to the system and also in the case that the state file gets corrupted or deleted, it can lead to conflicts and errors in the provisioning process.

### Getting Started: Basic Concepts

#### Providers

-   Providers are plugins that allow Terraform to interact with specific infrastructure resources.
-   Act as an interface between Terraform and the underlying infrastructure, translating the Terraform config into the appropriate API calls
-   Allows Terraform to manage resources across a wide variety of environments.
-   Each provider has its own set of resources and data sources that can be managed and provisioned using Terraform.

```
provider "oci" {
  tenancy_ocid     = "tenancy_ocid"
  user_ocid        = "user_ocid"
  fingerprint      = "fingerprint"
  private_key_path = "private_key_path"
  region           = "region"
}
```

#### Resources

-   Resources in Terraform refer to the components of infrastructure that Terraform is able to manage, such as virtual machines, virtual networks, DNS entries, pods, and others.
    -   Terraform can be used to create, update, and delete resources, managing dependencies between them and ensuring they are created in the correct order.
    -   You can also create explicit dependencies between some of the resources via `depends_on`
-   Each resource is defined by a type
    -   i.e. `aws_instance`, `google_dns_record`, `kubernetes_pod`, `oci_core_vcn`
    -   has a set of configurable properties, such as the instance size, vcn cidr, etc.
-   When referencing a resource from a configuration, using the syntax `<type>.<name>.<attribute>`

    ``` sh
     provider "aws" {
        region = "us-east-1"
    }

    resource "aws_vpc" "example" {
        cidr_block = "10.0.0.0/16"   <--- vpc being referenced
    }

    resource "aws_internet_gateway" "gw" {
        vpc_id = aws_vpc.example.id   <--- referencing the vpc above
    }
    
    ```

#### Commands

1. `terraform init`:

    - Initializes a working directory with Terraform configuration files.
    - First command executed after creating a new Terraform configuration or cloning an existing one from version control.
    - Downloads the specified provider and modules and saves them in a generated `.terraform` directory.

2. `terraform plan`:

    - generates an execution plan
    - shows a preview of changes Terraform intends to make

3. `terraform apply`:

    - executes the actions proposed.
    - If you don’t provide it a plan file, it will generate an execution plan when you are running the command, as if terraform plan ran.

4. `terraform destroy`:
    - delete all remote objects.

#### Implementation: Creating a VPC

1. Define the provider
2. Add the vpc configuration. Everything should be saved in a `.tf` file.

    ```
    provider "aws" {
      region = "us-east-1"
    }

    resource "aws_vpc" "example" {
      cidr_block = "10.0.0.0/16"
    }
    ```

3. Terraform essential commands

    1. `terraform init`:

        ```sh
        Initializing the backend...

        Initializing provider plugins...

        -   Finding latest version of hashicorp/aws...
        -   Installing hashicorp/aws v4.50.0...
        -   Installed hashicorp/aws v4.50.0 (signed by HashiCorp)

        Terraform has created a lock file .terraform.lock.hcl to record the provider
        selections it made above. Include this file in your version control repository
        so that Terraform can guarantee to make the same selections by default when
        you run "terraform init" in the future.

        Terraform has been successfully initialized!

        You may now begin working with Terraform. Try running "terraform plan" to see
        any changes that are required for your infrastructure. All Terraform commands
        should now work.

        If you ever set or change modules or backend configuration for Terraform,
        rerun this command to reinitialize your working directory. If you forget, other
        commands will detect it and remind you to do so if necessary.
        ```

    2. `terraform plan`:

        ```sh
        Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:

        -   create

        Terraform will perform the following actions:

        # aws_vpc.example will be created

        -   resource "aws_vpc" "example" {
            -   arn = (known after apply)
            -   cidr_block = "10.0.0.0/16"
            -   default_network_acl_id = (known after apply)
            -   default_route_table_id = (known after apply)
            -   default_security_group_id = (known after apply)
            -   dhcp_options_id = (known after apply)
            -   enable_classiclink = (known after apply)
            -   enable_classiclink_dns_support = (known after apply)
            -   enable_dns_hostnames = (known after apply)
            -   enable_dns_support = true
            -   enable_network_address_usage_metrics = (known after apply)
            -   id = (known after apply)
            -   instance_tenancy = "default"
            -   ipv6_association_id = (known after apply)
            -   ipv6_cidr_block = (known after apply)
            -   ipv6_cidr_block_network_border_group = (known after apply)
            -   main_route_table_id = (known after apply)
            -   owner_id = (known after apply)
            -   tags_all = (known after apply)
                }

        Plan: 1 to add, 0 to change, 0 to destroy.

        ───────────────────────────────────────────────────────────────────

        Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
        ```

    3. `terraform apply`:

        ```sh
        Terraform will perform the following actions:

        # aws_vpc.example will be created

        -   resource "aws_vpc" "example" {
            -   arn = (known after apply)
            -   cidr_block = "10.0.0.0/16"
            -   default_network_acl_id = (known after apply)
            -   default_route_table_id = (known after apply)
            -   default_security_group_id = (known after apply)
            -   dhcp_options_id = (known after apply)
            -   enable_classiclink = (known after apply)
            -   enable_classiclink_dns_support = (known after apply)
            -   enable_dns_hostnames = (known after apply)
            -   enable_dns_support = true
            -   enable_network_address_usage_metrics = (known after apply)
            -   id = (known after apply)
            -   instance_tenancy = "default"
            -   ipv6_association_id = (known after apply)
            -   ipv6_cidr_block = (known after apply)
            -   ipv6_cidr_block_network_border_group = (known after apply)
            -   main_route_table_id = (known after apply)
            -   owner_id = (known after apply)
            -   tags_all = (known after apply)
                }

        Plan: 1 to add, 0 to change, 0 to destroy.

        Do you want to perform these actions?
        Terraform will perform the actions described above.
        Only 'yes' will be accepted to approve.

        Enter a value: yes

        aws_vpc.example: Creating...
        aws_vpc.example: Creation complete after 3s [id=vpc-some_id]

        Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
        ```

    4. `terraform destroy`:

        ```sh
        aws_vpc.example: Refreshing state... [id=vpc-some_vpc]
        aws_internet_gateway.gw: Refreshing state... [id=igw-some_igw]

        Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:

        -   destroy

        Terraform will perform the following actions:

        # aws_internet_gateway.gw will be destroyed

        -   resource "aws_internet_gateway" "gw" {
            -   all parameters are specified here
                }

        # aws_vpc.example will be destroyed

        -   resource "aws_vpc" "example" {
            -   all parameters are specified here
                }

        Plan: 0 to add, 0 to change, 2 to destroy.

        Do you really want to destroy all resources?
        Terraform will destroy all your managed infrastructure, as shown above.
        There is no undo. Only 'yes' will be accepted to confirm.

        Enter a value: yes

        aws_internet_gateway.gw: Destroying... [id=igw-some_igw]
        aws_internet_gateway.gw: Destruction complete after 2s
        aws_vpc.example: Destroying... [id=vpc-some_vpc]
        aws_vpc.example: Destruction complete after 0s

        Destroy complete! Resources: 2 destroyed.
        ```

#### Data Sources and Outputs

##### Data Sources

-   A data source is a configuration object that retrieves data from an external source and can be used in resources as arguments when they are created or updated.
-   Data sources are defined in their respective providers
-   When referencing a data source, use the syntax `data.<type>.<name>.<attribute>`

    ```
    provider "aws" {
        region = "us-east-1"
    }

    data "aws_ami" "ubuntu" {
        filter {
            name   = "name"
            values = ["ubuntu-*"]  # starts with 'ubuntu-'
        }
        most_recent = true
    }

    output "ubuntu" {
        value = data.aws_ami.ubuntu.id  <--- specify what output value you want here
    }
    ```

##### Outputs

-   An output is a way to easily view the value of a specific data source, resource, local, or variable after Terraform has finished applying changes to infrastructure.
-   Used to expose different resources inside a module
-   Works independently from providers
-   Defined in the Terraform configuration file
-   `terraform output`: View outputs. Works only after `terraform apply`

        ``` sh
        # output of a terraform apply

        data.aws_ami.ubuntu: Reading...
        data.aws_ami.ubuntu: Read complete after 3s [id=ami-0f388924d43083179]

        Changes to Outputs:
        + ubuntu = "ami-0f388924d43083179"

        You can apply this plan to save these new output values to the Terraform state, without changing any real infrastructure.

        Do you want to perform these actions?
        Terraform will perform the actions described above.
        Only 'yes' will be accepted to approve.

        Enter a value: yes


        Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

        Outputs:

        ubuntu = "ami-0f388924d43083179"
        ```

> NOTE: In a `terraform apply`, the first state a resource goes through is creating, a data source is going through a reading state.

### Making things modular

[[tools.Infra.terraform.basics.variables]]
[[tools.Infra.terraform.basics.provisioners]]
[[tools.Infra.terraform.basics.operators]]
[[tools.Infra.terraform.basics.functions]]
[[tools.Infra.terraform.basics.files]]


---

### The Terraform State

Terraform state is a critical component of Terraform that enables users to define, provision, and manage infrastructure resources using declarative code. In this blog post, we will explore the importance of Terraform state, how it works, and best practices for managing it.

It is a json file that tracks the state of infrastructure resources managed by Terraform. By default, the name of the file is terraform.tfstate and whenever you update the first state, a backup is generated called terraform.tfstate.backup.

This state file is stored locally by default, but can also be stored remotely using a remote backend such as Amazon S3, Azure Blob Storage, Google Cloud Storage, or HashiCorp Consul. The Terraform state file includes the current configuration of resources, their dependencies, and metadata such as resource IDs and resource types. There are a couple of products that help with managing state and provide a sophisticated workflow around Terraform like Spacelift or Terraform Cloud.

How does it work?
When Terraform is executed, it reads the configuration files and the current state file to determine the changes required to bring the infrastructure to the desired state. Terraform then creates an execution plan that outlines the changes to be made to the infrastructure. If the plan is accepted, Terraform applies the changes to the infrastructure and updates the state file with the new state of the resources.

You can use the terraform state command to manage your state.

terraform state list: This command lists all the resources that are currently tracked by Terraform state.
terraform state show: This command displays the details of a specific resource in the Terraform state. The output includes all the attributes of the resource.
terraform state pull: This command retrieves the current Terraform state from a remote backend and saves it to a local file. This command is useful when you want to make manual operations in a remote state.
terraform state push: This command uploads the local Terraform state file to the remote backend. This command is useful after you made manual changes to your remote state.
terraform state rm: This command removes a resource from the Terraform state. This doesn’t mean the resource will be destroyed, it won’t be managed by Terraform after you’ve removed it.
terraform state mv: This command renames a resource in the Terraform state.
terraform state replace-provider: This command replaces the provider configuration for a specific resource in the Terraform state. This command is useful when switching from one provider to another or upgrading to a new provider version.
Supported backends
Amazon S3 Backend

The Amazon S3 backend is a popular choice for remote state storage. To configure the Amazon S3 backend, you will need to create an S3 bucket and an IAM user with permissions to access the bucket. Here is an example of how to configure the Amazon S3 backend in Terraform:

terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "terraform.tfstate"
    region         = "us-west-2"
  }
}
The S3 backend supports locking, but to do that you will need also need a dynamodb table. The table must have a partition key named LockID as a string. If this is not configured, state locking will be disabled.

terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "terraform.tfstate"
    region         = "us-west-2"
    dynamodb_table = "terraform-state-lock"
  }
}
Azure Blob Storage Backend

The Azure Blob Storage backend is similar to the Amazon S3 backend and provides a remote storage solution for Terraform state files. To configure the Azure Blob Storage backend, you will need to create an Azure Storage Account and a container to store the Terraform state file. This backend supports locking by default, so you won’t need to configure anything else for locking.

Here is an example of how to configure the Azure Blob Storage backend in Terraform:

terraform {
  backend "azurerm" {
    storage_account_name = "mytfstateaccount"
    container_name       = "tfstate"
    key                  = "terraform.tfstate"
  }
}
Google Cloud Storage Backend

The Google Cloud Storage backend is a popular choice for remote state storage for users of Google Cloud Platform. To configure the Google Cloud Storage backend, you will need to create a bucket and a service account with access to the bucket. Again there is no need to do anything else to enable locking. Here is an example of how to configure the Google Cloud Storage backend in Terraform:

terraform {
  backend "gcs" {
    bucket       = "my-terraform-state-bucket"
    prefix       = "terraform/state"
    credentials  = "path/to/credentials.json"
  }
}
HTTP Backend

It is a simple backend that can be useful for development or testing, but it is not recommended for production use because it does not provide the same level of security and reliability as other backends.

To configure the HTTP backend, you will need to have access to a web server that can serve files over HTTP or HTTPS. Here is an example of how to configure the HTTP backend in Terraform:

terraform {
  backend "http" {
    address = "https://my-terraform-state-server.com/terraform.tfstate"
  }
}
In this example, the address parameter specifies the URL of the Terraform state file on the web server. You can use any web server that supports HTTP or HTTPS to host the state file, including popular web servers like Apache or Nginx.

When using the HTTP backend, it is important to ensure that the state file is protected with appropriate access controls and authentication mechanisms. Without proper security measures, the state file could be accessed or modified by unauthorized users, which could lead to security breaches or data loss.

Remote State Data Source
The Terraform Remote State Data Source, like any other data source, retrieves existing information. This special data source, doesn’t depend on any provider, this allows you to retrieve state data from a previously deployed Terraform infrastructure. This can be useful if you need to reference information from one infrastructure to another.

To use a remote state data source in Terraform, you first need to configure the remote state backend for the infrastructure you want to retrieve data from. This is done in the backend block in your Terraform configuration as specified above. After you create your infrastructure for the first configuration, you can reference it in the second one using the remote data source.

data "terraform_remote_state" "config1" {
  backend = "local"

  config = {
    path = "../config1"
  }
}

resource "null_resource" "this" {
  provisioner "local-exec" {
    command = format("echo %s", data.terraform_remote_state.config1.outputs.var1)
  }
}
In the above example, we are supposing that we have a configuration in the directory “../config1” that has some Terraform code up and running. In that code, we have declared a “var1” output, that we are referencing in our null resource.

As mentioned in some of the previous articles, Terraform documentation is your best friend when it comes to understanding what you can do with a resource or data source, or what it exposes.

This data source exposes:


Best Practices
There are several best practices for managing Terraform state, including:

Use a remote backend: Storing the Terraform state file remotely provides several benefits, including better collaboration, easier access control, and improved resilience. Remote backends such as Amazon S3 or HashiCorp Consul can be used to store the state file securely and reliably.
Use locking: When multiple users are working on the same Terraform project, locking is necessary to prevent conflicts. Locking ensures that only one user can modify the state file at a time, preventing conflicts and ensuring changes are applied correctly. As shown before, there are many backends that support locking.
Use versioning: Your configuration should always be versioned, as this will make it easier to achieve an older version of the infrastructure if something goes wrong with the changes you are making.
Isolate state:
Don’t add hundreds of resources in the same state: Making a mistake with one resource can potentially hurt all your infrastructure
Have a single state file per environment: When making changes, it is usually a best practice to first make the change on a lower environment and after that promote it to the higher one
Use Terraform workspaces: Terraform workspaces allow users to manage multiple environments, such as development, staging, and production, with a single Terraform configuration file. Each workspace has its own state file, allowing changes to be made to each of them
5. Use modules: Versioned modules will make it easier to make changes to your code, hence changes will be easier to promote across environments, making operations to the state less painful.

Key Points
Terraform state is a critical component that enables users to define, provision, and manage infrastructure resources using declarative code. Terraform state ensures that resources are created, updated, or destroyed only when necessary and in the correct order. Remote backends such as Amazon S3, Azure Blob Storage, Google Cloud Storage, or HashiCorp Consul can be used to store the state file securely and reliably, while state file locks can prevent conflicts when multiple users are working with the same Terraform configuration. Consistent naming conventions and the terraform state command can help to ensure that Terraform state files are easy to manage and understand.

By following these best practices for managing Terraform state, users can ensure that their infrastructure resources are managed effectively and efficiently using Terraform.

---

Understanding how to use depends_on and the lifecycle block can help you better manage complex infrastructure dependencies and handle resource updates and replacements. In this post, I will provide an overview of what these features are, how they work, and best practices for using them effectively in your Terraform code.

Depends_on
The depends_on meta-argument in Terraform is used to specify dependencies between resources. When Terraform creates your infrastructure, it automatically determines the order in which to create resources based on their dependencies. However, in some cases, you may need to manually specify the order in which resources are created, and that's where depends_on comes in.

depends_on works on all resources, data sources, and also modules. You are going to most likely use depends_on whenever you are using null_resources with provisioners to accomplish some use cases.

Let’s look into one simple example:

resource "kubernetes_namespace" "first" {
  metadata {
    name = "first"
  }
}

resource "kubernetes_namespace" "second" {
  depends_on = [
    kubernetes_namespace.first
  ]
  metadata {
    name = "second"
  }
}
Even though these namespaces resources are the same, the one named “second” will get created after the one named “first”.

As the depends_on argument uses a list, this means that your resources can depend on multiple things before they are getting created.

resource "kubernetes_namespace" "first" {
  metadata {
    name = "first"
  }
}

resource "kubernetes_namespace" "second" {
  depends_on = [
    kubernetes_namespace.first,
    kubernetes_namespace.third
  ]
  metadata {
    name = "second"
  }
}

resource "kubernetes_namespace" "third" {
  metadata {
    name = "third"
  }
}

# kubernetes_namespace.first: Creating...
# kubernetes_namespace.third: Creating...
# kubernetes_namespace.third: Creation complete after 0s [id=third]
# kubernetes_namespace.first: Creation complete after 0s [id=first]
# kubernetes_namespace.second: Creating...
# kubernetes_namespace.second: Creation complete after 0s [id=second]
As we’ve made the second namespace depend also on the third now, you can see from the above apply output that the first and third race to get created first, and after they finished, the second one started the creation process.

What about depends_on on modules? It works exactly the same:

module "null" {
  depends_on = [
    resource.null_resource.this
  ]
  source = "./null"
}

resource "null_resource" "this" {
  provisioner "local-exec" {
    command = "echo this resource"
  }
}

# null_resource.this: Creating...
# null_resource.this: Provisioning with 'local-exec'...
# null_resource.this (local-exec): Executing: ["/bin/sh" "-c" "echo this resource"]
# null_resource.this (local-exec): this resource
# null_resource.this: Creation complete after 0s [id=1259986187217330742]
# module.null.null_resource.this: Creating...
# module.null.null_resource.this: Provisioning with 'local-exec'...
# module.null.null_resource.this (local-exec): Executing: ["/bin/sh" "-c" "echo this module"]
# module.null.null_resource.this (local-exec): this module
# module.null.null_resource.this: Creation complete after 0s [id=3893065594330030689]
As you see, the null resource from the module gets created after the other one. And of course, modules can even depend on other modules, but you got the drill.

Do I use depends_on now? Not that much, but back in the day on Terraform 0.10, whenever there were problems related to dependencies this was used to fix them.

Lifecycle Block
In Terraform, a lifecycle block is used to define specific behaviors for a resource during its lifecycle. This block is used to manage the lifecycle of a resource in Terraform, including creating, updating, and deleting resources.

The lifecycle block can be added to a resource block and includes the following arguments:

create_before_destroy: When set to true, this argument ensures that a new resource is created before the old one is destroyed. This can help avoid downtime during a resource update.
prevent_destroy: When set to true, this argument prevents a resource from being destroyed. This can be useful when you want to protect important resources from being accidentally deleted.
ignore_changes: This argument specifies certain attributes of a resource that Terraform should ignore when checking for changes. This can be useful when you want to prevent Terraform from unnecessarily updating a resource.
replace_triggered_by: This is relatively new, came up in Terraform 1.2, and it is used to replace a resource if any attributes of that resource have changed, or even other resources have changed. Also, if you use count or for_each on the resource, you can even retrigger the recreation if there is a change to an instance of that resource (using count.index or each.key)
To be honest, I’ve used prevent_destroy only once or twice, ignore_changes, and create_before_destroy a couple of times, and I’ve just found out about replace_triggered_by as I was writing the article.

Let’s see a lifecycle block in action:

resource "kubernetes_namespace" "first" {
  metadata {
    name = "first"
    labels = {
      color = "green"
    }
  }
  lifecycle {
    ignore_changes = [
      metadata[0].labels
    ]
  }
}

output "namespace_labels" {
  value = kubernetes_namespace.first.metadata[0].labels
}


# kubernetes_namespace.first: Creating...
# kubernetes_namespace.first: Creation complete after 0s [id=first]

# Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

# Outputs:

# namespace_labels = tomap({
#   "color" = "green"
# })
I’ve applied the above code for the first time and created the namespace with the above labels. In the lifecycle block, I’ve added the necessary configuration to ignore changes related to labels.

Now, let’s suppose someone comes in and tries to make a change to the labels:

resource "kubernetes_namespace" "first" {
  metadata {
    name = "first"
    labels = {
      color = "blue"
    }
  }
  lifecycle {
    ignore_changes = [
      metadata[0].labels
    ]
  }
}

output "namespace_labels" {
  value = kubernetes_namespace.first.metadata[0].labels
}


# kubernetes_namespace.first: Refreshing state... [id=first]

# No changes. Your infrastructure matches the configuration.

# Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.

# Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

# Outputs:

# namespace_labels = tomap({
#   "color" = "green"
# })
I’ve tried to reapply the code, but Terraform detects there is no change required due to the fact that we have the lifecycle block in action.

Let’s add to the lifecycle block a create_before_destroy option. With this option enabled it doesn’t mean that you won’t ever be able to destroy the resource, but whenever there is a change that dictates the resource has to be recreated, it will first create the new resource and after that delete the existing one.

With the namespace resource already created, I’ve changed its name to induce a breaking change:

# kubernetes_namespace.first must be replaced
+/- resource "kubernetes_namespace" "first" {
      ~ id = "first" -> (known after apply)

      ~ metadata {
          - annotations      = {} -> null
          ~ generation       = 0 -> (known after apply)
          ~ name             = "first" -> "second" # forces replacement
          ~ resource_version = "816398" -> (known after apply)
          ~ uid              = "684f7401-1554-46fb-b21f-8e49329e76fa" -> (known after apply)
            # (1 unchanged attribute hidden)
        }
    }

Plan: 1 to add, 0 to change, 1 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

kubernetes_namespace.first: Creating...
kubernetes_namespace.first: Creation complete after 0s [id=second]
kubernetes_namespace.first (deposed object 725799ef): Destroying... [id=first]
kubernetes_namespace.first: Destruction complete after 6s

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

Outputs:

namespace_labels = tomap({
  "color" = "blue"
})
As you can see from the Terraform apply output, another resource was created first and after that, the old one was deleted.

Overall, depends_on and the lifecycle block can help you in some edge-case situations you may get in with your IaC. I don’t really use them frequently, but sometimes they are a necessary evil.

