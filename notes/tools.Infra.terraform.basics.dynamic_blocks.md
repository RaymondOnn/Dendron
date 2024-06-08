---
id: buqb28qm7ufqxebi2foemnh
title: dynamic_blocks
desc: ''
updated: 1717012482115
created: 1717012463144
---
### Dynamic Blocks

- Dynamic Blocks in Terraform let you **repeat configuration blocks** inside of a resource based on a variable/local/expression that you are using inside of them. They make your configuration DRY (Don’t Repeat Yourself).
- They are essential in order to builf reusable modules!!

#### How Dynamic Blocks work

- In a dynamic block, you can use the following parameters:
  - `for_each (required)`: iterates over the value you are providing
  - `content (required)`: block containing the body of each block that you are going to create
  - `iterator (optional)`: temporary variable used as an iterator
  - `labels (optional)`: list of strings that define the block labels. Never used them, tbh.
- You can have nested dynamic blocks, or you can use dynamic blocks to avoid generating an optional block inside configurations.

#### An example

- This is just a simplified version taken out of Terraform’s documentation
- The `set` block, for a helm release, is used to add custom values to be merged with the yaml values file.

    ``` sh
    resource "helm_release" "example" {
        name       = "my-redis-release"
        repository = "https://charts.bitnami.com/bitnami"
        chart      = "redis"

        set {
            name  = "cluster.enabled"
            value = "true"
        }

        set {
            name  = "metrics.enabled"
            value = "true"
        }

        set {
            name  = "service.annotations.prometheus\\.io/port"
            value = "9127"
            type  = "string"
        }
    }
    ```

- Rewriting this using dynamic blocks:

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

- When you are not defining an iterator, your iterator name will be exactly the name of the block, in our case is set.
- Using an iterator:

  ``` sh
  # The dynamic block will change to:
  dynamic "set" {
      for_each = local.set
      iterator = i
      content {
        name  = i.key
        value = i.value.value
        type  = lookup(i.value, "type", null)
      }
    }
  ```  

- As mentioned before, you can use dynamic blocks to avoid generating blocks altogether, so to achieve this in our example, what we can do is just make the local variable empty:`
  ``` sh
  locals {
    set = {}
  }
  resource "helm_release" "example" {
    name       = "my-redis-release"
    repository = "<https://charts.bitnami.com/bitnami>"
    chart      = "redis"

    values = []

    dynamic "set" {
      for_each = local.set
      iterator = i
      content {
        name  = i.key
        value = i.value.value
        type  = lookup(i.value, "type", null)
      }
    }
  }
  ````

#### The OCI Security List Problem
A security list in Oracle Cloud Infrastructure is pretty similar to a network access control list in AWS. When I started building the initial network module for OCI, security groups were not available so the only way to restrict network-related rules was to use security lists. I was using Terraform 0.11 and in that version, there weren’t that many features available (no dynamic blocks, no for_each) so it was pretty hard to build a reusable module.

Because security list rules were embedded inside the security list resource as blocks, it was impossible to make something generic out of them.

So basically, there was no real module built, before dynamic blocks, but after they were released, this bad boy was created:

``` sh
resource "oci_core_security_list" "this" {
  for_each       = var.sl_params
  compartment_id = oci_core_virtual_network.this[each.value.vcn_name].compartment_id
  vcn_id         = oci_core_virtual_network.this[each.value.vcn_name].id
  display_name   = each.value.display_name

  dynamic "egress_security_rules" {
    iterator = egress_rules
    for_each = each.value.egress_rules
    content {
      stateless   = egress_rules.value.stateless
      protocol    = egress_rules.value.protocol
      destination = egress_rules.value.destination
    }
  }

  dynamic "ingress_security_rules" {
    iterator = ingress_rules
    for_each = each.value.ingress_rules
    content {
      stateless   = ingress_rules.value.stateless
      protocol    = ingress_rules.value.protocol
      source      = ingress_rules.value.source
      source_type = ingress_rules.value.source_type

      dynamic "tcp_options" {
        iterator = tcp_options
        for_each = (lookup(ingress_rules.value, "tcp_options", null) != null) ? ingress_rules.value.tcp_options : []
        content {
          max = tcp_options.value.max
          min = tcp_options.value.min
        }
      }
      dynamic "udp_options" {
        iterator = udp_options
        for_each = (lookup(ingress_rules.value, "udp_options", null) != null) ? ingress_rules.value.udp_options : []
        content {
          max = udp_options.value.max
          min = udp_options.value.min
        }
      }
    }
  }
}
```

- As you can see, in this one, I’m also using nested dynamics, but because I’m using lookups, you don’t even need to specify the “tcp_options” or “udp_options” inside the “ingress_rules” if you don’t want to specify them for one of your rules. This could’ve been done more elegantly using optionals.
