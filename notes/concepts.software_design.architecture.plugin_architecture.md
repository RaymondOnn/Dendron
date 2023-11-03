---
id: lcik30ayvvswnmhy4iik3q7
title: Plugin_architecture
desc: ''
updated: 1692326212902
created: 1692230081728
---

https://mwax911.medium.com/building-a-plugin-architecture-with-python-7b4ab39ad4fc
https://eli.thegreenplace.net/2012/08/07/fundamental-concepts-of-plugin-infrastructures
Concept of applying it to data pipelines: https://medium.com/omarelgabrys-blog/plug-in-architecture-dec207291800

## What is plugin architecture?

- The plug-in architecture consists of two components: 
  - a core system
  - plug-in modules.
- The main key design here is to allow us to  add, remove, and change features via plugins with little or no effect on the rest of the core system or other plug-in modules.
- To do so, the specific rules and processing are separate from the core system. 

## Why plugin architecture: Pros and Cons?

### Pros

- Since these plugins are independent, this gives the agility to be able to quickly change, remove, and add plugins.
- Reducing the headache of having a bunch of services, of plugins, talking to each other and handling failures as a result. The behaviour of the application is somehow can be predicted.
- Each plugin can be deployed, tested, and scaled separately.

### Cons

- Changing the core system might break or alter the plugin’s behaviour. Hence, It requires thoughtful design at first.
- You’ll end up with a complex core system, full of a bunch of if-else conditions, plugins independency is no longer a characteristic, and changing one line of code will require an arsenal of analysts, developers, and testers.

### Core system

- The core system defines **how the system operates and the basic business logic.**
  - For example. The generic workflow, such as how the data flow inside the application is defined. But, the steps involved inside that workflow is up to plugin. And so, all extending plugins will follow that generic flow providing their customized implementation.
- It also **handles special cases, applies special rules, and complex conditional processing**. These are the things that need to be enforced regardless of the extending plugin.
- In addition, it also contains the common code being used (or has to be used) by multiple plugins such as logging, security, versioning, UI components, database access, caching etc

### Plug-ins
- The plug-ins are stand-alone, independent components that contain specialized processing, additional features, and custom code that is meant to enhance or extend the core system to produce additional capabilities.
- Generally, plug-in modules should be independent of other plug-in modules. 

### Interaction between Core and Plug-in

- A common plugin system typically needs to define some form of contract/s that the plugins can extend and the core application can use as an abstraction layer for communication, this could be in the form of a package with some abstract classes. 
- Typically the application would using the following steps/phases to interact with the plugins:
  - ###### Discovery
    - This is the mechanism by which a running application can find out which plugins it has at its disposal. 
    - To “discover” a plugin, one has to look in certain places, and also know what to look for.
  - ###### Registration
    - This is the mechanism by which a plugin tells an application — “I’m here, ready to do work”. Admittedly, registration usually has a large overlap with discovery.
  - ###### Application Hooks
    - Hooks are also called “mount points” or “extension points”. These are the places where the plugin can “attach” itself to the application, signalling that it wants to know about certain events and participate in the flow. The exact nature of hooks is very much dependent on the application.
  - ###### Exposing application API to plugins
    - To make plugins truly powerful and versatile, the application needs to give them access to itself, by means of exposing an API the plugins can use.

## Example

#### `config.yaml`

``` yaml
registry:
  # Plugin registry settings example
  url: 'https://github.com/{name}/{repository}/releases/download/{tag}'
  name: ''
  repository: ''
  tag: 'latest'
logging:
  # Setting the log level: CRITICAL, ERROR, WARNING, INFO, DEBUG
  level: 'DEBUG'
plugins:
  # Packages to download from registry
  - advance-plugin
  - sample-plugin
```

#### Discovery

