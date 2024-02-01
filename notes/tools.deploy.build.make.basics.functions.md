---
id: 9ew8lc0iwqzzrld0p3ni34f
title: functions
desc: ''
updated: 1706577291536
created: 1706535765427
---

## [Functions](https://www.gnu.org/software/make/manual/html_node/Functions.html)

-   Functions allow you to do text processing in the makefile to compute the files to operate on or the commands to use in recipes.
-   You use a function in a function call, where you give the name of the function and some text (the arguments) for the function to operate on.
-   The result of the function’s processing is substituted into the makefile at the point of the call, just as a variable might be substituted.
-   To call a function:
    ```makefile
    $(function arguments)
    # or like this:
    ${function arguments}
    ```
    -  `function` is a function name; one of a short list of names that are part of make. Can also create your own functions by using the `call` function.
    - The `arguments` are the arguments of the function. 
      - They are separated from the function name by one or more spaces or tabs 
      - if there is more than one argument, then they are separated by commas. 
      - Such whitespace and commas are not part of an argument’s value.
      - If the arguments themselves contain other function calls or variable references, it is wisest to use the same kind of delimiters for all the references; write `$(subst a,b,$(x))`, not `$(subst a,b,${x})`. It's clearer, and because only one type of delimiter is matched to find the end of the reference.
      - Each argument is expanded before the function is invoked, unless otherwise noted below. The substitution is done in the order in which the arguments appear.

### Using Special Characters 
- There is no support for escaping characters with backslashes 
- To use special characters, you can hide them by putting them into variables.

    ``` makefile
    # Example: the subst function replaces each space with a comma, 
    # through the value of foo, and substitutes the result.

    comma:= ,
    empty:=
    space:= $(empty) $(empty)
    foo:= a b c
    bar:= $(subst $(space),$(comma),$(foo))
    # bar is now ‘a,b,c’.
    ```

- Characters you may need to hide include:
    - Commas
    - Initial whitespace in the first argument
    - Unmatched open parenthesis or brace
    - An open parenthesis or brace if you don’t want it to start a matched pair

### The functions

- `$(subst from,to,text)`: Performs a textual replacement on the text text: each occurrence of from is replaced by to. The result is substituted for the function call 
  - for e.g., `$(subst ee,EE,feet on the street)` produces the value `fEEt on the strEEt`.

- `$(patsubst pattern,replacement,text)`: Finds whitespace-separated words in text that match pattern and replaces them with replacement 
  - for e.g.`$(patsubst %.c,%.o,x.c.c bar.c)` produces the value `x.c.o bar.o`.

- `$(strip string)`: Removes leading and trailing whitespace from string and replaces each internal sequence of one or more whitespace characters with a single space. 
  - Thus, `$(strip a b c )` results in `a b c`.

- `$(findstring find,in)`: Searches 'in' for an occurrence of 'find'. If it occurs, the value is find; otherwise, the value is empty. 

- `$(filter pattern…,text)`: Returns all whitespace-separated words in text that do match any of the pattern words, removing any words that do not match. The patterns are written using ‘%’, just like the patterns used in the `patsubst` function.

- $(filter-out pattern…,text)
Returns all whitespace-separated words in text that do not match any of the pattern words, removing the words that do match one or more. This is the exact opposite of the filter function.

For example, given:

objects=main1.o foo.o main2.o bar.o
mains=main1.o main2.o
the following generates a list which contains all the object files not in ‘mains’:

$(filter-out $(mains),$(objects))
$(sort list)
Sorts the words of list in lexical order, removing duplicate words. The output is a list of words separated by single spaces. Thus,

$(sort foo bar lose)
returns the value ‘bar foo lose’.

Incidentally, since sort removes duplicate words, you can use it for this purpose even if you don’t care about the sort order.

$(word n,text)
Returns the nth word of text. The legitimate values of n start from 1. If n is bigger than the number of words in text, the value is empty. For example,

$(word 2, foo bar baz)
returns ‘bar’.

$(wordlist s,e,text)
Returns the list of words in text starting with word s and ending with word e (inclusive). The legitimate values of s start from 1; e may start from 0. If s is bigger than the number of words in text, the value is empty. If e is bigger than the number of words in text, words up to the end of text are returned. If s is greater than e, nothing is returned. For example,

$(wordlist 2, 3, foo bar baz)
returns ‘bar baz’.

$(words text)
Returns the number of words in text. Thus, the last word of text is $(word $(words text),text).

- `$(firstword names…)`: The argument names is regarded as a series of names, separated by whitespace. The value is the first name in the series. The rest of the names are ignored.
  - For e.g. `$(firstword foo bar)`; produces the result ‘foo’. 
  - Although `$(firstword text)` is the same as `$(word 1,text)`, the `firstword` function is retained for its simplicity.

$(lastword names…)
The argument names is regarded as a series of names, separated by whitespace. The value is the last name in the series.

For example,

$(lastword foo bar)
produces the result ‘bar’. Although $(lastword text) is the same as $(word $(words text),text), the lastword function was added for its simplicity and better performance.

Here is a realistic example of the use of subst and patsubst. Suppose that a makefile uses the VPATH variable to specify a list of directories that make should search for prerequisite files (see VPATH Search Path for All Prerequisites). This example shows how to tell the C compiler to search for header files in the same list of directories.

The value of VPATH is a list of directories separated by colons, such as ‘src:../headers’. First, the subst function is used to change the colons to spaces:

$(subst :, ,$(VPATH))
This produces ‘src ../headers’. Then patsubst is used to turn each directory name into a ‘-I’ flag. These can be added to the value of the variable CFLAGS, which is passed automatically to the C compiler, like this:

override CFLAGS += $(patsubst %,-I%,$(subst :, ,$(VPATH)))
The effect is to append the text ‘-Isrc -I../headers’ to the previously given value of CFLAGS. The override directive is used so that the new value is assigned even if the previous value of CFLAGS was specified with a command argument (see The override Directive).

---

-   Functions are blocks of code that can be reused.
-   The function name can be any string, but ideally a descriptive name that reflects the purpose of the function.
-   The function arguments are optional and can be used to pass parameters to the function.
-   To define a function:
    ```makefile
    define function-name
        command1
        command2
        ...
    endef
    ```
-   Calling a function can be done like this:

    ```makefile
    define print_anything
        @echo "\nMakefile: $(1)\n"
    endef

    test:
        $(call print_anything, "Testing a first function in a Makefile.")
    ```

    -   The custom function `print` is called with the keyword call inside `$()`.
    -   The parameters are passed to the function by specifying them inside those parenthesis separated by a comma.
    -   Makefile targets won’t complain when this comma has been forgotten.
    -   The parameter will be silently ignored and won’t be available inside the function. Thats one drawback of calling those functions and can lead to some time spend debugging weird issues.

#### The function `wildcard`

```makefile
$(wildcard *.c)
```

-   The wildcard function allows you to match files against a pattern and return a space-separated list of names of existing files that match one of the given file name patterns.
-   If no existing file name matches a pattern, then that pattern is omitted from the output of the wildcard function.
-   The results of the wildcard function are sorted. But again, each individual expression is sorted separately, so `$(wildcard _.c _.h)` will expand to all files matching `.c`, sorted, followed by all files matching `.h`, sorted.
