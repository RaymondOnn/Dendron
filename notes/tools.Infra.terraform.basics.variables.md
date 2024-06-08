---
id: ch7pw9nxsjc9pgfnf15diyt
title: variables
desc: ''
updated: 1717016636076
created: 1711770844845
---

<https://www.reddit.com/r/Terraform/comments/12tob5q/when_to_use_locals_vs_variables/>
.tfvars

### Variables and Locals

- Used to better organize your code, easily change it, and make your configuration reusable.

#### Variables

- Every variable will be declared with a variable block and referencing it is done using the syntax `var.<variable_name>`.

    ```sh
    resource "aws_instance" "web" {
        ami           = data.aws_ami.ubuntu.id
        instance_type = var.instance_type   #<--- referencing the variable here
    }

    # Declare variable called instance_type
    variable "instance_type" {
        description = "Instance Type of the variable"
        type        = string
        default     = "t2.micro"
    }
    ```

- If you happen to specify a value for a variable in multiple ways, Terraform will use the last value it finds, by taking into consideration their precedence order.
    1. using a default (this will be overwritten by any other options)
    2. using a `terraform.tfvars' file, a file in which you can add values to your variables
    3. using a `*.auto.tfvars file` ( '\*' is acting as a wildcard here)
    4. using `-var` or `-var-file` when running terraform plan/apply/destroy. When both options are used in the same command, the value will be taken from the last option.

##### Data types

- Supported types in Terraform:

  - Primitive:

    - String
    - Number

        ```sh

        variable "my_number" {
            description = "Number example"
            type        = number
            default     = 10
        }
        ```

    - Bool

        ```sh
        variable "my_bool" {
            description = "Bool example"
            type        = bool
            default     = false
        }
        ```

  - Complex — These types are created from other types:
    - List: `list(<data_type)`
      - Allows multiple elements of the same `data_type` within the list.
      - To access the elements, use `var.my_list_of_strings[index]`.
      - Keep in mind that lists start from 0.

        ```sh
        variable "my_list_of_strings" {
            description = "List of strings example"
            type        = list(string)
            default     = ["string1", "string2", "string3"]
        }
        ```

    - Set
    - Map: `map(<data_type>)`
      - Allows multiple `key:value pairs` of same `data_type`
      - To access the elements, use `var.my_map_of_strings[key]`. Similar to python dicts

        ```sh
        variable "my_map_of_strings" {
            description = "Map of strings example"
            type        = map(string)
            default = {
                key1 = "value1"
                key2 = "value2"
                key3 = "value"
            }
        }
            ```

    - Object: `object({})`
      - Allows multiple parameters, Can be of any data type. Similar to a map having more explicit types defined for the keys.
      - Same method of accessing elements as map

        ```sh
        variable "my_object" {
            description = "Object example"
            type = object({
                parameter1 = string
                parameter2 = number
                parameter3 = list(number)
            })
            default = {
                parameter1 = "value"
                parameter2 = 1
                parameter3 = [1, 2, 3]
            }
        }
        ```

    - Null — Usually represents absence, really useful in conditional expressions.
  - the `any` type
    - add whatever you want without caring about the type
    - Not recommended as it will make your code harder to maintain.

- Some other variables examples:

  - `map(object({}))`
    - works well with `for_each`
    - To access a property of an object in the map, use `var.my_map_of_objects["key"]["parameter"]`
    - if there are any other complex parameters defined you will have to go deeper.
    - `var.my_map_of_objects["elem1"]["parameter1"]` will return value.
    - `var.my_map_of_objects["elem1"]["parameter3"]["key1"]` will return value1.
    - Optional can be used on parameters inside object variables to give the possibility to omit that particular parameter and to provide a default value for it instead.

    ```sh
    variable "my_map_of_objects" {
    description = "Map(object) example"
        type = map(object({
            parameter1 = string
            parameter2 = bool
            parameter3 = map(string)
        }))
        default = {
            elem1 = {
                parameter1 = "value"
                parameter2 = false
                parameter3 = {
                    key1 = "value1"
                }
            }
            elem2 = {
                parameter1 = "another_value"
                parameter2 = true
                parameter3 = {
                    key2 = "value2"
                }
            }
        }
    }
    ```

  - `list(object({}))`
    - Great with dynamic blocks
    - To access a property of an object in the list use `var.my_list_of_objects[index]["parameter"]`.

        ```sh
        variable "my_list_of_objects" {
            description = "List(object) example"
            type = list(object({
                parameter1 = string
                parameter2 = bool
            }))
            default = [
                {
                    parameter1 = "value"
                    parameter2 = false
                },
                {
                    parameter1 = "value2"
                    parameter2 = true
                }
            ]
        }
        ```

> _NOTE_: You cannot reference other resources or data sources inside a variable, so you cannot say that a variable is equal to a resource attribute by using the type.name.attribute.

#### Locals

- a local variable assigns a name to an expression, making it easier for you to reference it, without having to write that expression a gazillion times.
- Defined in a locals block, and can have multiple local variables defined in a single local block.
- To reference, use `local.<local_variable_name>`.
- Unlike variables, inside of a local, allowed to define whatever resource or data source attribute you want.

    ```sh

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

    resource "aws_instance" "web" {
        ami           = data.aws_ami.ubuntu.id
        instance_type = local.instance_type
    }
    ```
