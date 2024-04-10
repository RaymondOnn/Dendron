---
id: n4hf87nshy5btscn26oh2ep
title: varlables
desc: ''
updated: 1710960185008
created: 1706535700043
---

> **Reference: https://www.gnu.org/software/make/manual/html_node/Quick-Reference.html**

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
-   The caret `^` symbol causes `MAKE` to interpret the next character literally. This is useful for inserting a new-line character. For example:

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

#### Variable Assignment
-  the `?=` operator only defines a macro if it is **not already defined** (like as an environment variable).