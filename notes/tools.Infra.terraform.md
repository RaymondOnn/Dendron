---
id: 1gqxivxjn9su38epd3johay
title: terraform
desc: ""
updated: 1717016435271
created: 1711702949703
---

- CLI Commands: <https://spacelift.io/blog/terraform-commands-cheat-sheet>
terraform refresh
terraform fmt


### What is Terraform

- a tool for building, changing and versioning infrastructre safely and efficiently
- Developed by Hashicorp to make creating and takeing care of infrastructure resources easier.
- Terraform uses HCL (Hashicorp Configuration Language) code to define its resources
- Platform-agnostic
- Terraform is stateful and keeps its state in a state file.
  - Uses it and your configuration, to determine the actions needed to reach the desired state of the resources.
  - ensures unnecessary resources is not created and helps to prevent errors and conflicts.
  - However, this adds complexity to the system and also in the case that the state file gets corrupted or deleted, it can lead to conflicts and errors in the provisioning process.

### Getting Started: Basic Concepts

#### Providers

- Providers are plugins that allow Terraform to interact with specific infrastructure resources.
- Act as an interface between Terraform and the underlying infrastructure, translating the Terraform config into the appropriate API calls
- Allows Terraform to manage resources across a wide variety of environments.
- Each provider has its own set of resources and data sources that can be managed and provisioned using Terraform.

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

- Resources in Terraform refer to the components of infrastructure that Terraform is able to manage, such as virtual machines, virtual networks, DNS entries, pods, and others.
  - Terraform can be used to create, update, and delete resources, managing dependencies between them and ensuring they are created in the correct order.
  - You can also create explicit dependencies between some of the resources via `depends_on`
- Each resource is defined by a type
  - i.e. `aws_instance`, `google_dns_record`, `kubernetes_pod`, `oci_core_vcn`
  - has a set of configurable properties, such as the instance size, vcn cidr, etc.
- When referencing a resource from a configuration, using the syntax `<type>.<name>.<attribute>`

    ```sh
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

- A data source is a configuration object that retrieves data from an external source and can be used in resources as arguments when they are created or updated.
- Data sources are defined in their respective providers
- When referencing a data source, use the syntax `data.<type>.<name>.<attribute>`

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

- An output is a way to easily view the value of a specific data source, resource, local, or variable after Terraform has finished applying changes to infrastructure.
- Used to expose different resources inside a module
- Works independently from providers
- Defined in the Terraform configuration file
- `terraform output`: View outputs. Works only after `terraform apply`

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

- Used to better organize your code, easily change it, and make your configuration reusable.

    ```sh
    # main.tf
    resource "aws_instance" "web" {
        ami           = data.aws_ami.ubuntu.id
        instance_type = var.instance_type   #<--- referencing the variable here
    }

    # variables.tf
    # Declare variable called instance_type
    variable "instance_type" {
        description = "Instance Type of the variable"
        type        = string
        default     = "t2.micro"
    }
    ```

> ..."Variables are for input. Things that you can change via inputs (like a vars file).
> Locals are "private". You can only change them by altering the code.
> Read the documentation from hashi regarding local variables. I think in there they describe it like so:
> Think of your terraform as a programming function. You have values you can pass to it. Those are like tf variables. Then, you have variables inside the function that do various things, but are not a part of the interface. Consumers of your function wouldn't know anything about those private internal variables. Those are like tf locals."...

[[tools.Infra.terraform.basics.provisioners]]

- Provisioners are used to run different commands or scripts on your local machine / remote machine, and also copy files from your local machine to a remote one, usually during the setup / teardown of resources

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

- A Null_Resource is commonly used to run scripts on a specified trigger.

[[tools.Infra.terraform.basics.operators]]
[[tools.Infra.terraform.basics.functions]]
[[tools.Infra.terraform.basics.files]]

[[tools.Infra.terraform.basics.state]]

- Terraform state file (`.tfstate`) is a json file that tracks the state of infrastructure resources managed by Terraform.
- Remote backends allow you to store the state file in a remote, shared store.

    ```sh
    # GCS Backend
    terraform {
        backend "gcs" {
            bucket       = "my-terraform-state-bucket"
            prefix       = "terraform/state"
            credentials  = "path/to/credentials.json"
        }
    }
    ```

[[tools.Infra.terraform.basics.order]]

- The `depends_on` meta-argument in Terraform is used to **specify dependencies between resources**.

    ```sh
    resource "kubernetes_namespace" "first" {
        metadata {
            name = "first"
        }
    }

    # making the second namespace depend also on the third now
    # Hence, the first and third race to get created first
    # After they finished, the second one started the creation process.

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

    Output:
    # kubernetes_namespace.first: Creating...
    # kubernetes_namespace.third: Creating...
    # kubernetes_namespace.third: Creation complete after 0s [id=third]
    # kubernetes_namespace.first: Creation complete after 0s [id=first]
    # kubernetes_namespace.second: Creating...
    # kubernetes_namespace.second: Creation complete after 0s [id=second]
    ```

- In Terraform, a `lifecycle` block is used to define specific behaviors for a resource during its lifecycle.

    ```sh
    resource "kubernetes_namespace" "first" {
    metadata {
        name = "first"
        labels = {
            color = "blue"  # <-- color changed from green to blue
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
    ```

[[tools.Infra.terraform.basics.dynamic_blocks]]

- Dynamic Blocks let you **repeat configuration blocks** inside of a resource based on a variable/local/expression that you are using inside of them

    ``` sh
    locals {
        set = {
            "cluster.enabled" = {
                value = true
            }
            "metrics.enabled" = {
                value = true
            }
            "service.annotations.prometheus\\.io/port" = {
                value = "9127"
                type  = "string"
            }
        }
    }

    resource "helm_release" "example" {
        name       = "my-redis-release"
        repository = "https://charts.bitnami.com/bitnami"
        chart      = "redis"

        values = []

        # using the `for_each` on the local variable creates the block three times.
        dynamic "set" {
            for_each = local.set
            content {
              name  = set.key
              value = set.value.value
              type  = lookup(set.value, "type", null) # set.type.value, null otherwise
            }
        }
    }
    ```
[[tools.Infra.terraform.basics.modules]]
---

---
