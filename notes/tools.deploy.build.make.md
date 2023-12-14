---
id: wgw781ouni3ue6lmz0ysu4e
title: make
desc: ''
updated: 1702338827303
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
-   When running MAKE, you can specify a different makefile name with the -f option. Otherwise, it assumes, by default, your makefile is called MAKEFILE and searches for it.
-   Similarly, we can also specify which rule to execute via the `make` command: `make <TARGET_NAME>`.
-   if target is not specified, MAKE will execute the first rule it finds in the makefile.
- Each line in a makefile target recipe  runs in a fresh context within its own shell session. 
  - This doesn't affect most recipes as they operate in the directory they need to by default. 
  - When it does, the workaround is to chain the commands together.
  - Alternatively, see `.ONESHELL`
  
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

### Functions

-   Functions are blocks of code that can be reused.
-   The function name can be any string, but ideally a descriptive name that reflects the purpose of the function.
-   The function arguments are optional and can be used to pass parameters to the function.
-   To define a function:
    ``` makefile
    define function-name
        command1
        command2
        ...
    endef
    ```
-   Calling a function can be done like this:

    ``` makefile
    define print_anything
        @echo "\nMakefile: $(1)\n"
    endef

    test:
        $(call print_anything, "Testing a first function in a Makefile.")
    ```

    -   The custom function print is called with the keyword call inside $().
    -   The parameters are passed to the function by specifying them inside those parenthesis separated by a comma.
    -   Makefile targets won’t complain when this comma has been forgotten.
    -   The parameter will be silently ignored and won’t be available inside the function. Thats one drawback of calling those functions and can lead to some time spend debugging weird issues.

#### The function `wildcard`

``` makefile
$(wildcard *.c)
```

-   The wildcard function allows you to match files against a pattern and return a space-separated list of names of existing files that match one of the given file name patterns.
-   If no existing file name matches a pattern, then that pattern is omitted from the output of the wildcard function.
-   The results of the wildcard function are sorted. But again, each individual expression is sorted separately, so `$(wildcard _.c _.h)` will expand to all files matching `.c`, sorted, followed by all files matching `.h`, sorted.

### Variables (also called macros)

-   A macro is a variable that MAKE expands into a string whenever MAKE encounters the macro in a makefile.
-   General format: `VARIABLE_NAME = <EXPANSION_TEXT>`

    ``` makefile
    # define a variable CC
    CC = gcc

    # Using the variable CC
    compile:
            $(CC) -o program program.c
    ```

-   Can also be defined using the `-D` command-line option.

    ``` makefile
    make -Dsourcedir=c:\projecta
    make -Dcommand="bcc32 -c"
    make -Dcommand=bcc32 option=-c
    ```

    -   No spaces are allowed before or after the equal sign =
    -   you can define more than one macro by separating the definitions with spaces.
    -   Note: Macros defined in makefiles overwrite macros defined at the command line.

-   There are also built-in variables, i.e. CC and CFLAGS, automatically set by Make. You can view the complete list of built-in variables by running make `--print-data-base`.

#### Special Variables

##### `.DEFAULT_GOAL`

-   Sets the default goal to be used if no targets were specified on the command line (see Arguments to Specify the Goals).
-   The `.DEFAULT_GOAL` variable allows you to
    -   discover the current default goal,
    -   restart the default goal selection algorithm by clearing its value,
    -   or to explicitly set the default goal.

```makefile
# Query the default goal.
ifeq ($(.DEFAULT_GOAL),)
  $(warning no default goal is set)
endif
>>> no default goal is set

.PHONY: foo
foo: ; @echo $@

$(warning default goal is $(.DEFAULT_GOAL))
>>> default goal is foo

# Reset the default goal.
.DEFAULT_GOAL :=

.PHONY: bar
bar: ; @echo $@

$(warning default goal is $(.DEFAULT_GOAL))
>>> default goal is bar

# Set our own.
.DEFAULT_GOAL := foo
>>> foo
```

-   Note that assigning more than one target name to .DEFAULT_GOAL is invalid and will result in an error.

#### Automatic Variables

-   These are set during the build process.
-   Some of the most common automatic variables include:
    -   `$@`: Represents the target of the current rule.
    -   `$(@D)`: Represents the directory the target of the current rule exist in.
    -   `$<`: Represents the first dependency of the current rule.
    -   `$^`: Represents all dependencies of the current rule.
    -   `$?`: Represents all dependencies that are newer than the target.
    -   `$*`: Represents the stem of a pattern rule. The stem is the part of the target that matches the % symbol in the pattern rule.

#### String Substitution in MAKE Macros

-   MAKE lets you temporarily substitute characters in a previously defined macro.
-   For example,
    -   if you define the following macro: `SOURCE = f1.cpp f2.cpp f3.cpp`
    -   you can substitute the characters `.obj` for the characters `.cpp` by using the following MAKE command: `$(SOURCE:.cpp=.obj)`
    -   NOTE: This substitution does not redefine the macro.
-   Rules for macro substitution:
    -   Syntax: `$(VARIABLE_NAME:ORIG_TEXT=NEW_TEXT)`.
    -   No space before or after the colon.
    -   Characters in original_text must exactly match the characters in the macro definition (text is case-sensitive).
-   MAKE also lets you use macros within substitution macros. For example:
    ``` makefile
    MYEXT=.C
    SOURCE=f1.cpp f2.cpp f3.cpp
    $(SOURCE:.cpp=$(MYEXT)) #Changes 'f1.cpp' to 'f1.C', etc.
    ```
-   The caret ^ symbol causes MAKE to interpret the next character literally. This is useful for inserting a new-line character. For example:

    -   Here, the caret tells MAKE to change each occurrence of .cpp to .C followed by the new-line character.

        ``` makefile
        # changes 'f1.cpp f2.cpp f3.cpp' to:
        # f1.C
        # f2.C
        # f3.C

        MYEXT=.C
        SOURCE=f1.cpp f2.cpp f3.cpp
        ($(SOURCE):.cpp=$(MYEXT)^
        )
        ```

#### Accepting Command Line Arguments

-   Suppose this makefile rule
    ``` makefile
    VAR = "default"
    action:
    	@echo $(VAR)
    ```
-   We can assign values to `$(VAR)` through the command line like this:
    ``` makefile
    make action VAR="value"
    >>> value
    ```

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

