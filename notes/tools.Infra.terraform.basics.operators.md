---
id: 1zc6ynmp49ue22b9w2xn8tc
title: operators
desc: ''
updated: 1716977672125
created: 1711945177201
---


### Count, For_Each, and Ternary operators

#### `COUNT`

-   Using `count` we can create multiple resources of the same type.
-   Every terraform resource supports the count block.
-   Count exposes a count.index object, which can be used similarly like an iterator in any programming language.

    ```sh
    # Create 5 namespaces in Kubernetes: ns1, ns2, ns3, ns4, ns5.
    # If count changed to 4, the last namespace, ns5 will be deleted after re-apply.

    resource "kubernetes_namespace" "this" {
        count = 5
        metadata {
            name = format("ns%d", count.index)
        }
    }
    ```

-   To reference a particular index of the resource, use `<type>.<name>[index]` i.e `kubernetes_namespace.this[0]` - `kubernetes_namespace.this[4]`.

-   Using `count.index` with functions and local/variables

    ```


    # Creates three namespaces called frontend, backend, and database.
    locals {
    namespaces = ["frontend", "backend", "database"]
    }

    resource "kubernetes_namespace" "this" {
    count = length(local.namespaces)
    metadata {
        name = local.namespaces[count.index]
    }
    }
    ```

-   Suppose you want to remove the backend namespace and keep only the other two.
    What is going to happen when you reapply the code?

    ```sh
    Plan: 1 to add, 0 to change, 2 to destroy.

    # Initially, we have the following resources:
    kubernetes_namespace.this[0] → frontend  #<--- No change
    kubernetes_namespace.this[1] → backend
    kubernetes_namespace.this[2] → database #<--- Had to be destroyed to be recreated as index 1

    # After we remove backend:
    kubernetes_namespace.this[0] → frontend
    kubernetes_namespace.this[1] → database
    ```

    > Imagine all the problems when recreating a namespace with a ton of things inside!!!

#### `FOR_EACH`

I am a big fan of using `for_each` on all of the resources, as you never know when you want to create multiple resources of the same kind.

-   `For_Each` can be used with map and set variables
-   `For_each` exposes one attribute called `each` which contains a key and value which can be referenced with `each.key` and `each.value`.
-   To use, you will reference an instance of your resource with `type.name[key]`.

    ```sh
    resource "kubernetes_namespace" "this" {
        for_each = var.namespaces #<-- iterating over variable "namespaces"
        metadata {
            name        = each.key # value on the laft side i.e. namespace1, namespace2, namespace3
            annotations = each.value.annotations
            labels      = each.value.labels
        }
    }

    # Defined a variable called namespaces. Iterating through it on 'kubernetes_namespace' resource.
    variable "namespaces" {
        type = map(object({
            # optional() allows fallback to default values i.e. it will be valid even if no value is provided
            annotations = optional(map(string), {})
            labels      = optional(map(string), {})
        }))
        default = {
            namespace1 = {}
            namespace2 = {
                labels = {
                    color = "green"
                }
            }
            namespace3 = {
                annotations = {
                    imageregistry = "https://hub.docker.com/"
                }
            }
            namespace4 = {
                labels = {
                    color = "blue"
                }
                annotations = {
                    imageregistry = "my_awesome_registry"
                }
            }
        }
    }

    # The entire configuration translates into:
    # namespace1 → will have no labels and no annotations
    # namespace2 → will have only labels
    # namespace3 → will have only annotations
    # namespace4 → will have both labels and annotations
    ```

-   Suppose you want to remove namespace2.
    What is going to happen when you reapply the code?
    -   Absolutely nothing.
    -   Unlike using list, removing an element of the map will not affect the others.
    -   this is why I will always vouch for for_each instead of count.

#### Ternary Operators

-   For conditionals, there is the ternary operators and the syntax is:

    ```sh
    condition ? val1 : val2
    ```

    -   if the condition is true, use val1
    -   if the condition is false, use val2

-   An example:

    ```sh
    # checking if local.use_local_name is equal to true
    # if true, local.name. Otherwise, namespace2.

    locals {
        use_local_name = false
        name           = "namespace1"
    }

    resource "kubernetes_namespace" "this" {
        metadata {
            name = local.use_local_name ? local.name : "namespace2"
        }
    }
    ```

##### Nested conditionals.

-   checking initially if `val2` is greater than `val1`:

    -   if it’s not, then nested_conditional will go to the last “:” and assign the value to `local.val1`
    -   if it is, then we are checking if `val3` is greater than `val2` and:
        -   if `val3` is greater than `val2` the value of nested \_conditional will be `val3`
        -   if `val3` is less than `val2` the value of nested_conditional will be `val2`

    ```sh
    locals {
        val1               = 1
        val2               = 2
        val3               = 3
        nested_conditional = local.val2 > local.val1 ? local.val3 > local.val2 ? local.val3 : local.val2 : local.val1
    }
    ```

-   These nested conditionals can get pretty hard to understand and usually if you see something that goes more than 3 or 4 levels,
-   there is almost always an error in judgment somewhere or you should do some changes to the variable or expression that you are using when you are building this as it will get almost impossible to maintain in the long run.

#### For loops and Ifs

-   for loops and ifs in Terraform are pretty similar to Python’s list comprehensions.

```sh
locals {
    list_var = range(5)  #<--- i.e. [0, 1, 2, 3, 4]
    map_var = {
        cat1 = {
            color = "orange",
            name  = "Garfield"
        },
        cat2 = {
            color = "blue",
            name  = "Tom"
        }
    }
    for_list_list = [for i in local.list_var : i * 2]
    for_list_map  = { for i in local.list_var : format("Number_%s", i) => i }

    for_map_list = [for k, v in local.map_var : k]
    for_map_map  = { for k, v in local.map_var : format("Cat_%s", k) => v }

    # Iterate through list_var and create a new list with elements > 2.
    for_list_list_if = [for i in local.list_var : i if i > 2]
    # Iterate through map_var and create a new map with all the element.color == orange.
    for_map_map_if   = { for k, v in local.map_var : k => v if v.color == "orange" }
}

```

> Note: you can build both lists and maps by using this type of loop.
>
> -   By starting the value with `[` you are creating a list
> -   by starting the value with `{` you are creating a map.
> -   The difference here is that for maps, you have to provide the `=>` attribute.

-   When iterating through a map variable,
    -   you are using a single iterator, you will actually cycle only through the values of the map,
    -   by using two, you will cycle through both keys and variables (the first iterator will be the key, the second iterator will be the value).
-   Examples of locals defined with for loops and ifs.

    ```sh
    for_list_list = [
        0,
        2,
        4,
        6,
        8,
    ]
    for_list_map = {
        "Number_0" = 0
        "Number_1" = 1
        "Number_2" = 2
        "Number_3" = 3
        "Number_4" = 4
    }
    for_map_list = [
        "cat1",
        "cat2",
    ]
    for_map_map = {
        "Cat_cat1" = {
            "color" = "orange"
            "name" = "Garfield"
        }
        "Cat_cat2" = {
            "color" = "blue"
            "name" = "Tom"
        }
    }
    for_list_list_if = [
        3,
        4,
    ]
    for_map_map_if = {
        "cat1" = {
            "color" = "orange"
            "name" = "Garfield"
        }
    }
    ```

-   `splat(*)`:
    -   help with providing a more concise way to reference some common operations that you would do with a `for`.
    -   works only on lists, sets, and tuples.
    -   for e.g., with a list of maps in the above format, you can build a list of all the names or ages from it, by using the `splat` operator.