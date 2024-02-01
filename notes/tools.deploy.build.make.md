---
id: wgw781ouni3ue6lmz0ysu4e
title: make
desc: ''
updated: 1706535335451
created: 1699784509486
---

https://medium.com/gitconnected/the-art-of-makefiles-a-technical-guide-to-automated-building-6bb43fefe1ed
https://docwiki.embarcadero.com/RADStudio/Alexandria/en/MAKE.EXE#About_makefiles
https://medium.com/@seniorbrogrammer/my-makefile-design-for-python-api-project-cc5740da5c69

TODO: include clause: https://www.youtube.com/watch?v=buxd7FcyAU4&list=PLF47AC7312BE0799A&index=5
TODO: Add argument variables
https://stackoverflow.com/questions/1789594/how-do-i-write-the-cd-command-in-a-makefile

### Makefile

-   In software development, a build refers to the process of compiling and combining the various source code files, libraries, and assets into a single executable program or application.
-   The build process includes tasks such as compiling the code, linking libraries, and creating the final binary or executable file.
-   A makefile is an ASCII file that contains the set of instructions that MAKE uses to build a certain project.

### Rules and Dependencies

-   To define a rule:

    ``` makefile
    target: dependencies
        command1
        command2
        ...
    ```

-   A Makefile consists of rules that specify how to build a target i.e such as an executable or a library.
-   Targets have dependencies which are files that are used as input to create the target
-   Execution of rules is done via the make command
-   When running `MAKE`, you can specify a different makefile name with the `-f` option. Otherwise, it assumes, by default, your makefile is called `Makefile` and searches for it.
-   Similarly, we can also specify which rule to execute via the `make` command: `make <TARGET_NAME>`.
-   if target is not specified, `MAKE` will execute the first rule it finds in the makefile.
- Each line in a makefile target recipe  runs in a fresh context within its own shell session. 
  - This doesn't affect most recipes as they operate in the directory they need to by default. 
  - When it does, the workaround is to chain the commands together.
  - Alternatively, see `.ONESHELL`


### Dependencies 

#### Types of Prerequisites
- Two different types of prerequisites understood 
    1. normal prerequisites
     2. order-only prerequisites. 

#### Normal Prerequisites
- Ensure that if a target’s prerequisite is updated, then the target should also be updated.
- A normal prerequisite makes two statements: 
    1. it imposes an order in which recipes will be invoked
       - the recipes for all prerequisites of a target will be completed before the recipe for the target is started. 
     2. it imposes a dependency relationship
       - if any prerequisite is newer than the target, then the target is considered out-of-date and must be rebuilt.

#### Order-only Prerequistes
- Ensure that a prerequisite is built before a target, but without forcing the target to be updated if the prerequisite is updated. 
- Order-only prerequisites are never checked when determining if the target is out of date; even order-only prerequisites marked as phony (see Phony Targets) will not cause the target to be rebuilt.
- Specified by placing a pipe symbol (|) in the prerequisites list
    - Any prerequisites to the left of the pipe symbol are normal; any prerequisites to the right are order-only:
        ``` makefile
        targets : normal-prerequisites | order-only-prerequisites
        ```
    - The normal prerequisites section may of course be empty. 
    - Also, you may still declare multiple lines of prerequisites for the same target: they are appended appropriately (normal prerequisites are appended to the list of normal prerequisites; order-only prerequisites are appended to the list of order-only prerequisites). 
    - Note that if you declare the same file to be both a normal and an order-only prerequisite, the normal prerequisite takes precedence (since they have a strict superset of the behavior of an order-only prerequisite).

#### An example
- Consider an example where your targets are to be placed in a separate directory, and that directory might not exist before make is run. 
- In this situation, you want the directory to be created before any targets are placed into it but, because the timestamps on directories change whenever a file is added, removed, or renamed, we certainly don’t want to rebuild all the targets whenever the directory’s timestamp changes. 
- One way to manage this is with order-only prerequisites: make the directory an order-only prerequisite on all the targets:

    ``` makefile
    OBJDIR := objdir
    OBJS := $(addprefix $(OBJDIR)/,foo.o bar.o baz.o)

    $(OBJDIR)/%.o : %.c
            $(COMPILE.c) $(OUTPUT_OPTION) $<

    all: $(OBJS)

    $(OBJS): | $(OBJDIR)

    $(OBJDIR):
            mkdir $(OBJDIR)
    ```
    - Now the rule to create the objdir directory will be run, if needed, before any ‘.o’ is built, but no ‘.o’ will be built because the objdir directory timestamp changed.


> **Tip: Specifying Inputs**
>
> -   Instead of listing all the file dependencies, we can run a shell command and have its output substituted into the Makefile
> -   To find all the source files, you might do something like: find src/ -type f
>     ```makefile
>     out/image-id: $(shell find src -type f)  # here we track all files within src folder
>     	image_id="example.com/my-app:$$(pwgen -1)"
>     	docker build --tag="$${image_id}
>     	echo "$${image_id}" > out/image-id
>     ```

#### Chaining Rules

```makefile
# Makefile
hello:
    echo "Hello World"

world: hello
    echo "This is a chained command"

---
make world
>>> echo "Hello World"
>>> Hello World
>>> echo "This is a chained command"
>>> This is a chained command
```

-   To build more than a single target, use a phony target in your makefile.

### Phony Targets

-   a target that doesn't take into consideration for execution any file that matches its name
-   .PHONY is used to mark targets as phony.
-   Usually used for recipes (i.e. a series of build commands) to be executed when you make an explicit request such as for cleaning up the build files, such as object files and executables, installing the build files, such as the executable and libraries, to a specified location:
-   `.PHONY` is itself a target for the `make` command and and it denotes labels that do not represent files of the project.
-   General format:

    -   a `.PHONY` element followed by a label and then the same label used as a target for the Makefile.

    ``` makefile
    # define as phony target
    .PHONY: clean
    clean:
        rm -f $(OBJECTS) $(EXECUTABLE)
    ```

-   When invoked via the `make` command (i.e. `make clean` in this case) it will execute the specified command (i.e. `rm -f $(OBJECTS) $(EXECUTABLE)` in this case).
-   Without `.PHONY`, if there was a file named clean, the `make clean` would be bound by the presence of this file and executed only if that file was updated.
-   With `.PHONY`, the `clean` file is not taken into consideration and `clean` will always run as expected, regardless of the presence of a file named `clean`.

-   TIP: Another way to mark the targets as phony is to have them all in place list rather than above each of them like:

    ``` makefile
    clear-cache:
        rm -rf cache/*
    clear-build:
        rm -rf build/*

    .PHONY: clear-cache clear-build
    ```

#### Why Phony Targets?

> **TL;DR**
> If you strictly want a target that generates no files, tell Make by saying it is `.PHONY`:
> `` makefile
	test:
	> npm run test
	.PHONY: test  # Make will not look for a file named `test` on the file system
	``

-   Improved Build Speed: As I said, a phony target is not tied to a specific file, so Make won’t waste time checking if the target is up to date. This can significantly speed up your build time.
-   Consistent Build Results: A phony target ensures that its recipe will always be executed, regardless of whether there are files with the same name as the target. This guarantees consistent build results.
-   Cleaner Build Process: Phony targets allow you to encapsulate a series of build commands into a convenient target. For example, you can create a clean target that removes all the generated files, making it easier to clean up your build environment.
-   Improved Build Process Control: Phony targets allow you to define targets for specific purposes, such as installing the build files to a specified location or running tests. This gives you more control over the build process and makes it easier to automate certain tasks.

Another example is for defining a target for

### Phony Targets v2

-   A phony target forces MAKE to build multiple targets in a makefile.
-   A phony target is one that is not really the name of a file. It is just a name for some commands to be executed when you make an explicit request.
-   When specifying a phony target, the dependency line lists all the targets you want to build (a symbolic target basically uses linked dependencies to build more than one target). (See "linked dependencies" in the Default MAKE Actions section in this topic.)
-   Order of execution is not guaranteed

    ``` makefile
    AllFiles: file1.exe file2.exe  #Note that AllFiles has no commands
    file1.exe: file1.obj
        bcc32 file1.obj
    file2.exe: file2.obj
        bcc32 file2.obj

    # `make AllFiles` now builds both FILE1.EXE and FILE2.EXE:
    ```

-   Rules for symbolic targets
    -   Do not type a line of commands after the phony target line.
    -   A phony target must have a unique name; it cannot be the name of a file in your current directory.
    -   Phony target names must follow the operating system rules for naming files.




### Pattern Rules: Simplifying targets and dependencies

-   Pattern rules are a way to match target files to source files using wildcard characters, making it easier to define dependencies for a large number of similar files.
-   Let’s consider a simple example.

    -   `%.o: %.c`: A pattern rule, where %.o matches any target that ends with .o, and %.c matches the corresponding source file ending in .c.

    ``` makefile
    # for .o targets that require a .c file
    %.o: %.c
        $(CC) $(CFLAGS) -c -o $@ $<
    ```

-   Pattern rules make it easier to define dependencies for similar files, without having to write separate rules for each file.
-   Pattern rules imply a range of possible values. To ensure that certain dependencies are available, it might be best to explicitly list out the dependencies

### Conditionals and Flow Control

#### If Statements

-   General format:
    ``` makefile
    ifdef VAR
        recipe1
    else
        recipe2
    endif
    ```
    ``` makefile
    ifeq ($(wildcard file.txt),)
        recipe1
    else
        recipe2
    endif
    ```

#### Double-Colon Rules

-   Make also provides double-colon rules, which allow you to define multiple recipes for the same target. The first recipe to successfully execute stops the execution of the other recipes:

``` makefile
target ::
recipe1
target ::
recipe2
```

### Snippets

-   Creates a help menu using comments

``` makefile
.PHONY: help
help: ## Show this help
	@egrep -h '\s##\s' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'

hi:
	@echo "a"

bye: hi
	@echo "b"

read: ## Reads from script
	@./echo.sh

copy: ## Create a copy
	@cp ./echo.sh ./echo1.sh

---

make
>>> copy                 Create a copy
>>> help                 Show this help
>>> read                 Reads from script

```

