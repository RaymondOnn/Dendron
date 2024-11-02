---
id: lxs4n3ds8afysz6g3qkjdup
title: state
desc: ''
updated: 1722957924423
created: 1716963945844
---

<https://blog.gruntwork.io/how-to-manage-terraform-state-28f5697e68fa>

### The Terraform State

- Terraform state is a critical component of Terraform that enables users to define, provision, and manage infrastructure resources using declarative code.
- It is a json file that tracks the state of infrastructure resources managed by Terraform.
- By default, the name of the file is `terraform.tfstate` and whenever you update the first state, a backup is generated called `terraform.tfstate.backup`.
- Stored locally by default, but can also be stored remotely using a remote backend such as Amazon S3, Azure Blob Storage, Google Cloud Storage, or HashiCorp Consul.
- The Terraform state file includes the current configuration of resources, their dependencies, and metadata such as resource IDs and resource types. There are a couple of products that help with managing state and provide a sophisticated workflow around Terraform like Spacelift or Terraform Cloud.

#### How does it work?

1. When Terraform is executed, it reads the configuration files and the current state file to determine the changes required to bring the infrastructure to the desired state.
2. Terraform then creates an execution plan that outlines the changes to be made to the infrastructure.
3. If the plan is accepted, Terraform applies the changes to the infrastructure and updates the state file with the new state of the resources.

#### The `terraform state` command

- The `terraform state` command is used to manage your state.
  - `terraform state list`: Lists **all the resources that are currently tracked** by Terraform state.
  - `terraform state show`: Displays the details of a specific resource in the Terraform state. The output includes all the attributes of the resource.
  - `terraform state pull`: Retrieves the current Terraform state from a remote backend and saves it to a local file.
    - Useful when you want to make manual operations in a remote state.
  - `terraform state push`: Uploads the local Terraform state file to the remote backend.
    - Useful after you made manual changes to your remote state.
  - `terraform state rm`: Removes a resource from the Terraform state. This doesn’t mean the resource will be destroyed, it won’t be managed by Terraform after you’ve removed it.
  - `terraform state mv`: Renames a resource in the Terraform state.
  - `terraform state replace-provider`: Replaces the provider configuration for a specific resource in the Terraform state.
    - Useful when switching from one provider to another or upgrading to a new provider version.

#### Remote backends

- Remote backends allow you to store the state file in a remote, shared store.

##### Amazon S3 Backend

- The Amazon S3 backend is a popular choice for remote state storage.
- To configure the Amazon S3 backend, you will need to create an S3 bucket and an IAM user with permissions to access the bucket.

  ```
  # Here is an example of how to configure the Amazon S3 backend in Terraform:
  terraform {
    backend "s3" {
      bucket         = "my-terraform-state-bucket"
      key            = "terraform.tfstate"
      region         = "us-west-2"
    }
  }
  ```

- The S3 backend supports locking, but to do that you will need also need a dynamodb table.
- The table must have a partition key named LockID as a string. If this is not configured, state locking will be disabled.

  ```
  terraform {
    backend "s3" {
      bucket         = "my-terraform-state-bucket"
      key            = "terraform.tfstate"
      region         = "us-west-2"
      dynamodb_table = "terraform-state-lock"
    }
  }
  ```

##### Azure Blob Storage Backend

- To configure the Azure Blob Storage backend, you will need to create an Azure Storage Account and a container to store the Terraform state file.
- This backend supports locking by default, so you won’t need to configure anything else for locking.

  ```
  terraform {
    backend "azurerm" {
      storage_account_name = "mytfstateaccount"
      container_name       = "tfstate"
      key                  = "terraform.tfstate"
    }
  }
  ```

##### Google Cloud Storage Backend

- To configure the Google Cloud Storage backend, you will need to create a bucket and a service account with access to the bucket.
- Again there is no need to do anything else to enable locking.

  ```
  terraform {
    backend "gcs" {
      bucket       = "my-terraform-state-bucket"
      prefix       = "terraform/state"
      credentials  = "path/to/credentials.json"
    }
  }
  ```

##### HTTP Backend

- It is a simple backend that can be useful for development or testing, but it is not recommended for production use because it does not provide the same level of security and reliability as other backends.
- To configure the HTTP backend, you will need to have access to a web server that can serve files over HTTP or HTTPS. Here is an example of how to configure the HTTP backend in Terraform:

  ```
  terraform {
    backend "http" {
      address = "https://my-terraform-state-server.com/terraform.tfstate"
    }
  }
  ```

  - In this example, the address parameter specifies the URL of the Terraform state file on the web server. You can use any web server that supports HTTP or HTTPS to host the state file, including popular web servers like Apache or Nginx.
- When using the HTTP backend, it is important to ensure that the state file is protected with appropriate access controls and authentication mechanisms.
- Without proper security measures, the state file could be accessed or modified by unauthorized users, which could lead to security breaches or data loss.

#### Remote State Data Source

- The Terraform Remote State Data Source, like any other data source, retrieves existing information.
- This special data source, doesn’t depend on any provider, this allows you to retrieve state data from a previously deployed Terraform infrastructure.
- Useful if you need to reference information from one infrastructure to another.

- To use a remote state data source in Terraform,
  - Configure the remote state backend for the infrastructure you want to retrieve data from.
  - This is done in the `backend` block in your Terraform configuration as specified above.
  - After you create your infrastructure for the first configuration, you can reference it in the second one using the remote data source.

  ```
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
  ```

In the above example, we are supposing that we have a configuration in the directory “../config1” that has some Terraform code up and running. In that code, we have declared a “var1” output, that we are referencing in our null resource.

As mentioned in some of the previous articles, Terraform documentation is your best friend when it comes to understanding what you can do with a resource or data source, or what it exposes.

This data source exposes:

#### Best Practices

There are several best practices for managing Terraform state, including:

1. **Use a remote backend**:
     - Storing the Terraform state file remotely provides several benefits, including better collaboration, easier access control, and improved resilience.
     - Remote backends such as Amazon S3 or HashiCorp Consul can be used to store the state file securely and reliably.
2. **Use locking**:
    - When multiple users are working on the same Terraform project, locking is necessary to prevent conflicts.
    - Locking ensures that only one user can modify the state file at a time, preventing conflicts and ensuring changes are applied correctly. As shown before, there are many backends that support locking.
3. **Use versioning**:
   - Your configuration should always be versioned, as this will make it easier to achieve an older version of the infrastructure if something goes wrong with the changes you are making.
4. **Isolate state**:
   1. **Don’t add hundreds of resources in the same state**: Making a mistake with one resource can potentially hurt all your infrastructure
   2. **Have a single state file per environment**: When making changes, it is usually a best practice to first make the change on a lower environment and after that promote it to the higher one
   3. **Use Terraform workspaces**: Terraform workspaces allow users to manage multiple environments, such as development, staging, and production, with a single Terraform configuration file. Each workspace has its own state file, allowing changes to be made to each of them
5. **Use modules**: Versioned modules will make it easier to make changes to your code, hence changes will be easier to promote across environments, making operations to the state less painful.
