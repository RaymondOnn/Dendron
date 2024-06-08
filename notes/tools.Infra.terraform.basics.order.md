---
id: 6ml8h9e1avwlnxgw2bz1j5l
title: order
desc: ''
updated: 1717012328717
created: 1717009364098
---

Understanding how to use depends_on and the lifecycle block can help you better manage complex infrastructure dependencies and handle resource updates and replacements.

### `Depends_on`

- The `depends_on` meta-argument in Terraform is used to **specify dependencies between resources**.
- While Terraform does try to determine the order in which to create resources based on their dependencies, `depends_on` allows you to manually specify the order in which resources are created.
- `depends_on` works on all resources, data sources, and also modules.
- You are going to most likely use `depends_on` whenever you are using `null_resources` with provisioners to accomplish some use cases.

    ```sh
    # the one named “second” will get created after the one named “first”.

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
    ```

- As the `depends_on` argument uses a list, this means that your resources can depend on multiple things before they are getting created.

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

- What about depends_on on modules? It works exactly the same:

    ```sh
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

    # NOTE: the null resource from the module gets created after the other one.
    Output:
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
    ```

### Lifecycle Block

- In Terraform, a lifecycle block is used to define specific behaviors for a resource during its lifecycle.
- This block is used to manage the lifecycle of a resource in Terraform, including creating, updating, and deleting resources.
- The lifecycle block can be added to a resource block and includes the following arguments:
  - `create_before_destroy`:
    - When set to true, this argument ensures that a new resource is created before the old one is destroyed.
    - This can help avoid downtime during a resource update.
  - `prevent_destroy`:
    - When set to true, this argument prevents a resource from being destroyed.
    - Useful when you want to protect important resources from being accidentally deleted.
  - `ignore_changes`:
    - Specifies certain attributes of a resource that Terraform should ignore when checking for changes.
    - Useful when you want to prevent Terraform from unnecessarily updating a resource.
  - `replace_triggered_by`:
    - Used to replace a resource if any attributes of that resource have changed, or even other resources have changed.
    - if you use `count` or `for_each` on the resource, you can even retrigger the recreation if there is a change to an instance of that resource (using `count.index` or `each.key`)

#### An Example

```sh
resource "kubernetes_namespace" "first" {
    metadata {
        name = "first"
        labels = {
            color = "green"
        }
    }
    lifecycle {
        ignore_changes = [  # <-- ignore changes related to labels
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
```

- Now, let’s suppose someone comes in and tries to make a change to the labels:

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

- Adding to the lifecycle block a `create_before_destroy` option.
  - With this option enabled, whenever there is a change that dictates the resource has to be recreated, it will first create the new resource and after that delete the existing one.
- With the namespace resource already created, I’ve changed its name to induce a breaking change:
- Note from the `Terraform apply` output, another resource was created first and after that, the old one was deleted.

    ``` sh
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

    # Plan: 1 to add, 0 to change, 1 to destroy.

    # Do you want to perform these actions?
    # Terraform will perform the actions described above.
    # Only 'yes' will be accepted to approve.

    # Enter a value: yes

    # kubernetes_namespace.first: Creating...
    # kubernetes_namespace.first: Creation complete after 0s [id=second]
    # kubernetes_namespace.first (deposed object 725799ef): Destroying... [id=first]
    # kubernetes_namespace.first: Destruction complete after 6s

    # Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

    # Outputs:

    # namespace_labels = tomap({
    #   "color" = "blue"
    # })
    ```
