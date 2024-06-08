---
id: jbw5bm945ybn0ow6yij2ghu
title: redirection
desc: ''
updated: 1715883554487
created: 1715352742168
---

### tl`dr

- File descriptor redirection
    ``` bash
    # redirect stderr to stdout
    2>&1
    # redirect stdout to stderr
    1>&2
    ```
- Redirection to a file
    - `stdout` -> `file`
    ``` bash
    >file
    1>file  # (same thing; the 1 is implied above)
    ```
    - `stderr` -> `file`
    ```bash
    2>file
    ```
    - [`stdout`, `stderr`] -> `file` (4 ways)
    ```bash
    # Think of the `&` symbol in this 1st example as meaning "1 AND 2", since it 
    # redirects both stdout (file descriptor 1) AND stderr (file descriptor 2)
    # to `file`
    &>file       # recommended in bash      <===
    >&file       # not recommended
    >file 2>&1   # universal and fine       <===
    1>file 2>&1  # exact same as just above <===
    ```
---
### Pipeing

- Redirect output from one command to another in Linux i.e. from stdout to another process / file / command
- The `|` symbol is used to link commands together, with redirection moving from left to right
- There are unnamed pipes and named pipes

``` bash
ps aux | grep firefox%d
```

### Redirection
- A way to redirection the input / output of a command to a file or another command
- `>`: used for output redirection. Start writing from start of file 
  - `>>`: for appending to existing content. Start writing from end of file
- `<`: used for input redirection

    ``` bash
    # Redirect the output to Newfile.txt until you input the word 'END' 
    cat > Newfile.txt << END
    ```
#### Combining both together

``` bash
# storing output from ls command into file via cat command
ls -l | cat > newtext.txt

## >> implies append
echo "append this" | cat >> newtext.txt
```
### Data Streams

-   Allows us to control where normal output and errors are printed and user input is accepted
-   3 types of data streams
    -   standard input (`stdin`)
    -   standard output (`stdout`): output printed to the screen that is not error (exit code 0)
    -   standard error (`stderr`): output printed to the screen that is error (exit code 1)

> **File descriptors**
> - An abstract representation used for accessing a file or input/output resource
> - In Linux, an example of file descriptors are the numbers that represent input, output and error streams
>     ``` bash
>     # File descriptor reminder:
>     0 = stdin
>     1 = stdout
>     2 = stderr
>     ```

#### I/O redirection
- `/dev/null`: Anything moved/copied into `/dev/null` is deleted
    ```bash
    find /etc -type f

    # To filter out all errors, we can do this:
    # "2>" is instructed to redirect anything of exit code 2 to /dev/null
    find /etc -type f 2> /dev/null

    # Saving stdout in result.txt
    # Saving stderr in error.txt
    find /etc -type f 1> ~/result.txt 2> ~/error.txt
    ```


#### File descriptor redirection
- `2>&1` is an I/O redirection operator used in Bash that **redirects the stderr stream to the same destination as the stdout stream**. 
- It merges the error output with the regular output, making it easier to capture and handle errors.
    ``` bash
    command 2>&1
    # ‘command’ is the command that is being executed
    # ‘2>&1’ is the I/O redirection operator that redirects stderr to stdout.
    ```
-  Especially useful in scripts that require error handling, where you need to know if a command executed successfully or encountered an error.
    ``` bash
    #!/bin/bash
    
    ls /not_a_directory > /dev/null 2>&1   
    if [ $? -ne 0 ]; then
        echo "Error: Directory not found"
    fi
    
    # /dev/null: discarding output (exit code) from ls
    # 2>&1: redirect stderr to stdout, which allows if statment to read it
    ```
> **What's the difference between `2>1` vs `2v&1`?**
> - `2>&1` redirects standard error to whatever standard output currently points at
> - `2>1` redirects standard error into a file called 1.
> - In the context of a file redirect, when it is the next token immediately after a `>` or `<`, `&` indicate that the next token is a **file descriptor**
> - Note that in other contexts it means something else. 


#### Redirecting Standard Output and Standard Error
https://stackoverflow.com/questions/24793069/what-does-do-in-bash
https://www.gnu.org/software/bash/manual/bash.html#Redirecting-Standard-Output-and-Standard-Error
- 4 ways in bash to redirect both `stdout` and `stderr` to `file`
    1. Recommended method in bash
        ``` bash
        # Think of the `&` symbol here as meaning "1 AND 2", since it redirects
        # both stdout (file descriptor 1) AND stderr (file descriptor 2) to `file`
        &>file
        ```
    2. It works, but `file` may NOT expand to a number or to `-`. 
        ``` bash
        >&file
        ```
    3. The universal way to do it in or outside of bash
        ``` bash
        # first redirect stdout to
        # file, and then redirect stderr to stdout
        >file 2>&1
        ```
    4. Exact same as 3 above
        ``` bash
        1>file 2>&1
        ```
> **So, &>file means "redirect both stdout and stderr to file."**



---
Examples:
``` bash
# print "hey 2 " to stdout.
# - Note: the `"%s "` format specifier gets applied to each input argument
#   thereafter. So, calling `printf "%s " "hello" "world"` is as though you had
#   called `printf "%s %s " "hello" "world"`.
printf "%s " "hey" 2

# redirect stdout to `file`; "file" now contains "hey 2 "
printf "%s " "hey" 2 >file
printf "%s " "hey" 2 1>file  # (same thing)

# redirect stderr to `file`; "file" remains empty since no stderr was printed
printf "%s " "hey" 2 2>file

# redirect BOTH stdout and stderr to `file`
printf "%s " "hey" 2 &>file
printf "%s " "hey" 2&>file        # don't do this! (same thing in this case, 
                                  # but looks awkward without the space before 
                                  # the `&`)
printf "%s " "hey" 2 >file 2>&1   # (same thing)
printf "%s " "hey" 2 1>file 2>&1  # (same thing)
```
####  "Weird" examples that we will present as "puzzles" and to teach and test understanding

``` bash
# print "hey 2 " to stdout, redirecting stdout to a file named "1", then to "2",
# then to "3", then to "4", then to "5". Ultimately, `>5` overrides all
# previous stdout redirections, resulting in 5 files being created
# (named "1", "2", "3", "4", and "5"), with **only file "5"** containing
# the "hey 2 " text, and all other files being empty. Read the contents of all 
# files all at once with `grep '' *`.
printf "%s " "hey" 2 >1 >2 >3 >4 >5
# OR, exact same thing:
printf "%s " "hey" 2 1>1 1>2 1>3 1>4 1>5

# read the contents of all files so you can see that only file "5" above has
# "hey 2 " in it
grep '' *

# print "hey " to a file named "5", while also creating empty files
# named "1", "2", "3", and "4". Note: `21>1` is a bit nonsensical in this case.
# It redirects file descriptor 21 to a file named "1". That doesn't really do
# anything. All the redirections thereafter redirect file descriptor 1 (stdout)
# to a file with a number for a name, as specified. 
printf "%s " "hey" 21>1 1>2 1>3 1>4 1>5

# print "hey " to a file named "5", while creating empty files
# named "1", "2", "3", and "4". stderr gets redirected to the file named "1",
# but it also ends up empty since no stderr output was produced by this command.
printf "%s " "hey" 2>1 1>2 1>3 1>4 1>5

# Print some error output to a file named "1", since stderr gets redirected to
# it. Stdout gets ultimately redirected to a file named "5", but no stdout
# output is printed since `--invalid_arg` is not a valid argument. The stderr
# error message printed to the file named "1" is:
#       bash: printf: --: invalid option
#       printf: usage: printf [-v var] format [arguments]
printf --invalid_arg "%s " "hey" 2>1 1>2 1>3 1>4 1>5

# print "hey 2 " to a file named "5", while also creating empty files
# named "1", "2", "3", and "4". Initially, both stdout and stderr get
# redirected to the file named "1", via `&>1`, but then the stdout redirection
# gets overridden repeatedly until the last one that "sticks" is the stdout
# redirection to the file named "5" via `1>5`.
printf "%s " "hey" 2 &>1 1>2 1>3 1>4 1>5
printf "%s " "hey" 2&>1 1>2 1>3 1>4 1>5     # (same as above, but even more
                                            # awkward-looking)
printf "%s " "hey" 2&>1 >2 >3 >4 >5         # (same as above)
printf "%s " "hey" 2 &>1 >2 >3 >4 >5        # (same as above)
printf "%s " "hey" 2 >1 2>&1 >2 >3 >4 >5    # (same as above)
printf "%s " "hey" 2 1>1 2>&1 >2 >3 >4 >5   # (same as above; reminder: `1>1` 
        # redirects stdout (file descriptor 1) to a file named "1", whereas
        # `2>&1` redirects stderr (file descriptor 2) to stdout 
        # (file descriptor 1); make sure you understand that by now)

# (NOT the same as above! See this example & description previously a few
# examples up)
printf "%s " "hey" 2>1 1>2 1>3 1>4 1>5
```