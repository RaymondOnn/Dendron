---
id: 1ii6nipjbvvozsjz8q1kufi
title: files
desc: ''
updated: 1712196682181
created: 1712196682181
---


#### Files
- Apart from the .tf and .tfvars files, you can use json, yaml, or whatever other file types that support your needs.

##### Reading Files

- For the following examples, let’s suppose we are using this yaml file:
  ``` yaml
  namespaces:
    ns1:
      annotations:
        imageregistry: "https://hub.docker.com/"
      labels:
        color: "green"
        size: "big"
    ns2:
      labels:
        color: "red"
        size: "small"
    ns3:
      annotations:
        imageregistry: "https://hub.docker.com/"
  ```
- Reading a file only if it exists

  ``` sh
  # fileexists checks if the file exists and returns true or false based on the result. 
  locals {
    my_file = fileexists("./my_file.yaml") ? file("./my_file.yaml") : null
  }

  output "my_file" {
    value = local.my_file
  }
  ```

- Reading the yaml file and using it in a configuration if it exists
  ``` sh

  # checking if the file exists
  # if yes, yamldecode on the loaded string to create a map variable 
  # otherwise use a variable for that. 
  locals {
    namespaces = fileexists("./my_file.yaml") ? yamldecode(file("./my_file.yaml")).namespaces : var.namespaces
  }

  output "my_file" {
    value = local.namespaces
  }

  resource "kubernetes_namespace" "this" {
    for_each = local.namespaces  #<-- passing local variable to for_each
    metadata {
      name        = each.key
      annotations = lookup(each.value, "annotations", {})
      labels      = lookup(each.value, "labels", {})
    }
  }

  variable "namespaces" {
    type = map(object({
      annotations = optional(map(string), {})
      labels      = optional(map(string), {})
    }))
    default = {
      ns1 = {}
      ns2 = {}
      ns3 = {}
    }
  }
  ```

Example Terraform plan:
``` sh
# kubernetes_namespace.this["ns1"] will be created
  + resource "kubernetes_namespace" "this" {
      + id = (known after apply)

      + metadata {
          + annotations      = {
              + "imageregistry" = "https://hub.docker.com/"
            }
          + generation       = (known after apply)
          + labels           = {
              + "color" = "green"
              + "size"  = "big"
            }
          + name             = "ns1"
          + resource_version = (known after apply)
          + uid              = (known after apply)
        }
    }

  # kubernetes_namespace.this["ns2"] will be created
  + resource "kubernetes_namespace" "this" {
      + id = (known after apply)

      + metadata {
          + generation       = (known after apply)
          + labels           = {
              + "color" = "red"
              + "size"  = "small"
            }
          + name             = "ns2"
          + resource_version = (known after apply)
          + uid              = (known after apply)
        }
    }

  # kubernetes_namespace.this["ns3"] will be created
  + resource "kubernetes_namespace" "this" {
      + id = (known after apply)

      + metadata {
          + annotations      = {
              + "imageregistry" = "https://hub.docker.com/"
            }
          + generation       = (known after apply)
          + name             = "ns3"
          + resource_version = (known after apply)
          + uid              = (known after apply)
        }
    }
```    
- Using `templatefile` on a yaml file and use its configuration if it exists
    ``` yaml
    # We can go further on this example and add some variables that we will change with templatefile. 
    # To do that, we must first make some changes to our initial yaml file.
    namespaces:
      ns1:
        annotations:
          imageregistry: ${image_registry_ns1}
        labels:
          color: "green"
          size: "big"
      ns2:
        labels:
          color: ${color_ns2}
          size: "small"
      ns3:
        annotations:
          imageregistry: "https://hub.docker.com/"
    ```      
  - The only change that we will have to do for the above code will be a change to the locals:
    ``` sh
    # Those two variables defined inside the yaml file using the ${} syntax, will be changed with the ones from the templatefile functions.
    
    locals {
      namespaces = fileexists("./my_file.yaml") ? yamldecode(templatefile("./my_file.yaml", { image_registry_ns1 = "ghcr.io", color_ns2 = "black" })).namespaces : var.namespaces
    }
    ```

##### `Fileset`
- The fileset function helps with identifying all files inside a directory that respect a pattern.
  ``` sh
  # Shows all the yaml files, inside the current directory
  # Outputs a list of all those files. 

  locals {
    yaml_files = fileset(".", "*.yaml")
  }
  ```
  - you can use the file function to load the content of these files as strings. 
  - You can take them one by one, using list indexes
  - or use a `for` loop and group them together in something that makes sense.

- Grouping multiple yaml files together in a single variable:
  ``` sh
  locals {
    namespaces = merge([for my_file in fileset(".", "*.yaml") : yamldecode(file(my_file))["namespaces"]]...)
  }
  output "namespaces" {
    value = local.namespaces
  }
  ```
  - As the files are exactly the same, only the names of the namespaces are different, this will result in:
    ``` sh
    namespaces = {
      "ns1" = {
        "annotations" = {
          "imageregistry" = "https://hub.docker.com/"
        }
        "labels" = {
          "color" = "green"
          "size" = "big"
        }
      }
      "ns2" = {
        "labels" = {
          "color" = "red"
          "size" = "small"
        }
      }
      "ns3" = {
        "annotations" = {
          "imageregistry" = "https://hub.docker.com/"
        }
      }
      "ns4" = {
        "annotations" = {
          "imageregistry" = "https://hub.docker.com/"
        }
        "labels" = {
          "color" = "green"
          "size" = "big"
        }
      }
      "ns5" = {
        "labels" = {
          "color" = "red"
          "size" = "small"
        }
      }
      "ns6" = {
        "annotations" = {
          "imageregistry" = "https://hub.docker.com/"
        }
      }
      "ns7" = {
        "annotations" = {
          "imageregistry" = "https://hub.docker.com/"
        }
        "labels" = {
          "color" = "green"
          "size" = "big"
        }
      }
      "ns8" = {
        "labels" = {
          "color" = "red"
          "size" = "small"
        }
      }
      "ns9" = {
        "annotations" = {
          "imageregistry" = "https://hub.docker.com/"
        }
      }
    }
    ```

##### Other Examples
- You can get filesystem-related information using these key expressions:
  - `path.module`: 
    - Returns the path of the current module being executed. 
    - Useful for accessing files or directories that are relative to the module being executed.
  - `path.root`: 
    - Returns the root directory of the current Terraform project. 
    - Useful for accessing files or directories located at the project's root.
  - `path.cwd`: 
    - Returns the current working directory where Terraform is being executed before any chdir operations happened. 
    - Useful for accessing files or directories that are relative to the directory where Terraform is running from.
- There are some other file functions that can be leveraged in order to accommodate some use cases, but to be honest I’ve used them only once or twice. Still, I believe mentioning them, will bring some value.
  - `basename`: takes a path and returns everything apart from the last part of it
    - for e.g. `basename("/Users/user1/hello.txt")` will return `hello.txt`.

  - `dirname`: behaves exactly opposite to basename, returns all the directories until the file
    - for e.g. `dirname("/Users/user1/hello.txt")` will return `/Users/user1`

  - `pathexpand`: takes a path that starts with a ~ and expands this path adding the home of the logged in user. If the path, doesn’t use a ~ this function will not do anything
    - for e.g. You are logged in as user1 on a Mac: `pathexpand("~/hello.txt")` will return `/Users/user1/hello.txt`
  - `filebase64`: reads the content of a file and returns it as base64 encoded text.
  - `abspath`: takes a string containing a filesystem path and returns the absolute path