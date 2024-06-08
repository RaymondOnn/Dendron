---
id: 5go07ey0xvjho3ins2gcr7y
title: modules
desc: ''
updated: 1717014165409
created: 1717014162095
---

### Modules

- Terraform modules are one of the most important features that Terraform has to offer.
- They make your code reusable, can be easily versioned and shared with others, and act as blueprints for your infrastructure.
- Similar concept wise to classes in OOP.
  - In OOP, you define a class and after that, you can create multiple objects out of it.
  - The same goes for Terraform modules, you define the module, and after that, you can reuse it as many times as you want.

#### Why should you use modules?

- There are several reasons to use Terraform modules in your IaC projects:
  - **Code Reusability**:
    - Avoiding the duplication of code through the reuse of configurations across multiple environments or projects.
    - More maintainable and easier to update.
  - **Separation of Concerns**:
    - Separates your infrastructure into smaller, more focused units.
    - Results in cleaner, more organized code that is easier to understand and manage.
  - **Versioning**:
    - Support versioning and can be shared across teams, making it easier to collaborate and maintain consistency across your organization’sinfrastructure.
  - **Simplified Configuration**:
    - By encapsulating complexity within modules, you can simplify your root configurations, making them easier to read and understand.
  - **Faster Development**:
    - With modules, you can break down your infrastructure into smaller, reusable components.
    - This modular approach accelerates development, as you can quickly build upon existing modules rather than starting from scratch for each new resource or environment.
  - **Scalability**:
    - Build scalable infrastructure by allowing you to replicate resources or environments easily.
    - By reusing modules, you can ensure that your infrastructure remains consistent and manageable even as it grows in size and complexity.

#### Minimal module structure

- A typical module should contain the following files:
  - `main.tf`: Contains the core resource declarations and configurations for the module.
  - `variables.tf`: Defines input variables that allow users to customize the module's behavior.
  - `outputs.tf`: Specifies output values that the module returns to the caller, providing information about the created resources.
  - `README.md`: Offers documentation on how to use the module, including descriptions of input variables and outputs.
- When building modules, it is recommended to create examples for those modules.
  - In each module, create an `examples` folder containing at least a `main.tf` where an object is created for that module.
  - `README.md`: Leverage `terraform-docs` to get the documentation automated, but also explain what the module does, how to leverage the examples and deep dive into why I took some decisions related to the code.

#### Module Example: Simple module for generating config maps in Kubernetes

- This module code will create how many namespaces and config maps you want in your Kubernetes cluster.
- You can even create your config maps in existing namespaces, as you are not required to create namespaces if you don’t want to.

  ``` sh
  # Module main code

  resource "kubernetes_namespace" "this" {
    for_each = var.namespaces
    metadata {
      name        = each.key
      labels      = each.value.labels
      annotations = each.value.annotations
    }
  }

  resource "kubernetes_config_map" "this" {
    for_each = var.config_maps
    metadata {
      name        = each.key
      namespace   = each.value.use_existing_namespace ? each.value.namespace : kubernetes_namespace.this[each.value.namespace].metadata[0].name
      labels      = each.value.labels
      annotations = each.value.annotations
    }

    data        = each.value.data
    binary_data = each.value.binary_data
  }

  # Module Variable code

  variable "namespaces" {
    description = "Namespaces parameters"
    type = map(object({
      labels      = optional(map(string), {})
      annotations = optional(map(string), {})
    }))
    default = {}
  }

  variable "config_maps" {
    description = "Config map parameters"
    type = map(object({
      namespace              = string
      labels                 = optional(map(string), {})
      annotations            = optional(map(string), {})
      use_existing_namespace = optional(bool, false)
      data                   = optional(map(string), {})
      binary_data            = optional(map(string), {})
    }))
  }

  # Module outputs code

  output "config_maps" {
    description = "Config map outputs"
    value       = { for cfm in kubernetes_config_map.this : cfm.metadata[0].name => { "namespace" : cfm.metadata[0].namespace, "data" : cfm.data } }
  }
  ```

- Using the module. Taking advantage of optionals, to avoid passing parameters in some of the cases.
- Here, I am creating one namespace and one config map into that namespace and I am also outputting the config maps.

  ``` sh
  # example main.tf code

  provider "kubernetes" {
    config_path = "~/.kube/config"
  }

  module "config_maps" {
    source = "../"
    namespaces = {
      ns1 = {
        labels = {
          color = "green"
        }
      }
    }

    config_maps = {
      cf1 = {
        namespace = "ns1"
        data = {
          api_host = "myhost:443"
          db_host  = "dbhost:5432"
        }
      }
    }
  }

  # example outputs.tf code

  output "config_maps" {
    description = "Config map outputs"
    value       = module.config_maps.config_maps
  }

- Example `terraform apply`
  ``` cmd
  Do you want to perform these actions?
    Terraform will perform the actions described above.
    Only 'yes' will be accepted to approve.

    Enter a value: yes

  module.config_maps.kubernetes_namespace.this["ns1"]: Creating...
  module.config_maps.kubernetes_namespace.this["ns1"]: Creation complete after 0s [id=ns1]
  module.config_maps.kubernetes_config_map.this["cf1"]: Creating...
  module.config_maps.kubernetes_config_map.this["cf1"]: Creation complete after 0s [id=ns1/cf1]

  Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

  Outputs:

  config_maps = {
    "cf1" = {
      "data" = tomap({
        "api_host" = "myhost:443"
        "db_host" = "dbhost:5432"
      })
      "namespace" = "ns1"
    }
  }
  ```

You can check out the repository and also the generated Readme.md file here.

#### Publishing Modules

- You can easily publish your modules to a Terraform registry.
- To share your module with the community, you can easily leverage Terraform’s Public Registry.
- However, if you have an account with a sophisticated deployment tool such as Spacelift, you can take advantage of the private registry it offers and take advantage of the built-in testing capabilities.

#### Minimal Best Practices

- If you are just starting to build Terraform module, take into consideration the following best practices:
  - **Keep modules focused**: Each module should have a specific purpose and manage a single responsibility. Avoid creating overly complex modules that manage multiple unrelated resources.
  - **Use descriptive naming**: Choose clear and descriptive names for your modules, variables, and outputs. This makes it easier for others to understand and use your module.
  - **Document your modules**: Include a README.md file in each module that provides clear instructions on how to use the module, input variables, and output values. In addition to this, use comments in your Terraform code to explain complex or non-obvious code.
  - **Version your modules**: Use version tags to track changes to your modules and reference specific versions in your configurations. This ensures that you’re using a known and tested version of your module, and it makes it easier to roll back to a previous version if needed.
  - **Test your modules**: Write and maintain tests for your modules to ensure they work as expected. The terraform validate and terraform plan commands can help you identify configuration errors, while other tools like Spacelift’s built-in module testing or Terratest can help you write automated tests for your modules.
