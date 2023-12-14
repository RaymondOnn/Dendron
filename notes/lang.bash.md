---
id: hba08e2flrm6n8y31riuh70
title: bash
desc: ""
updated: 1700675042719
created: 1694732979398
---

-   [Math Functions](https://www.youtube.com/watch?v=YDnp2oEiF64&list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w&index=4&ab_channel=LearnLinuxTV)
-   <https://www.youtube.com/playlist?list=PLT98CRl2KxKGj-VKtApD8-zCqSaN2mD4w>

### What is BASH?

-   A shell language
-   **B**ourne **A**gain **SH**ell
-   Easy commands

### Why BASH?

```diff
+ Most used shell
+ Comes with Linux, other OS
- No OOP
- Difficult syntax compared to Python
- Newer tools like Ansible
```

### Basic Commands

-   `echo $SHELL`: Find out what is the default shell of the system
-   `which bash`: Find out what command to use to start using bash

## Data Streams

-   Allows us to control where normal output and errors are printed and user input is accepted
-   types of data streams
    -   std output: output printed to the screen that is not error (exit code 0)
    -   std err

```bash
find /etc -type f

# Anything moved/copied into /dev/null is deleted
# "2>" is instructed to redirect anything of exit code 2 to /dev/null
# Hence all errors are hidden
find /etc -type f 2> /dev/null

```

## Here document

### What is a Here document

-   In Linux, here document (also commonly referred to as heredoc) refers to a special block of code that contains multi-line strings that will be redirected to a command.
-   On the other hand, here string is a simpler version of heredoc, offering somewhat similar functionality, albeit not as powerful as the latter.

### Here Document Syntax

-   A heredoc consists of the ‘<<‘ redirection operator, followed by a delimiter token.
-   After the delimiter token, lines of string can be defined to form the content.
-   Finally, the delimiter token is placed at the end to serve as the termination.
-   The delimiter token can be any value as long as it is unique enough that it won’t appear within the content.
-   For example, we can create a heredoc that consists of several lines of simple string and have it output by the command cat:
    ```bash
    cat <<EOF  # <--- Define "EOF" as delimiter
    This is line1
    Another line
    Finally 3rd line
    EOF       # <--- Content is whatever is sandwiched between the two delimiters
    ```

#### Tab Suppression

-   To suppress the tab indentations in the output, we can prefix the redirection operator ‘<<‘ with a dash symbol.
-   However, white spaces will not be suppressed even with the dash prefix.

    ```bash
    cat <<-EOF
        This message is indented
            This message is double indented
    EOF
    >>> This message is indented
    >>> This message is double indented
    ```

#### Parameter Substitution

-   We can also parameterize a heredoc by using variables, thereby making it dynamic.
-   For example, we can change the output of a heredoc based on the value of a parameter:
    ```bash
    cat <<EOF
    Hello ${USER}
    EOF
    ```

#### Command Substitution

-   heredoc also supports command substitution in its content.
-   For example, let’s say we want to customize the output such that it always print the current date and time:
    ```bash
    cat <<EOF
    Hello! It is currently: $(date)
    EOF
    ```

#### Passing Arguments to Function With Here Document

-   We can also make use of heredoc to pass arguments to a function that otherwise requires interactive user input.
-   For example, let’s consider the following function:

    ```bash
    # Waits for the user’s input to capture the value for variable username and passphrase
    LoginToModule()
    {
        read -p "Username: " username
        read -p "Passphrase: " passphrase
        echo "Obtained input ${username} and ${passphrase}"
    }

    LoginToModule
    >>> Username: baeldung
    >>> Passphrase: secret
    >>> Obtained input baeldung and secret

    # Alternatively, we can construct a heredoc to pass both values
    LoginToModule <<EOF
    adminuser
    adminpassphrase
    EOF
    ```

#### Escape Special Characters in Here Document

-   By default, the special characters like `$`, `\`, and <code>\`</code> are interpreted in heredoc so that functionality like parameter and command substitution could work. To escape them, we can prefix the special character with a backslash `\`:
  ```bash
  cat <<EOF
  Some Special Characters: \$ \\ \`
  EOF
  ```
- In the case that we want to escape all the special characters, we can either quote the delimiter token or prefix the delimiter token with a backslash.
- The three different ways of escaping all the special characters are:

  ```bash
  # Method 1:
  cat <<'EOF'
  Some Special Characters: $ \ ` EOF

  # Method 2:
  cat <<"EOF"
  Some Special Characters: $ \ `
  EOF

  # Method 3:
  cat <<\EOF
  Some Special Characters: $ \ `
  EOF
  ```
- NOTE: When all the special characters are escaped, parameter and command substitution will no longer work. The reason is that the special characters that are used in parameter and command substitution will be escaped, not interpreted.

#### Disable Block of Code Using Here Document
In a shell script, one way to disable a block of code is to prefix every line of the code with a ‘#’ to make them into comments. However, we can do it much more efficiently using heredoc with the dummy command ‘:’. For example, we can disable several lines of code in our shell script:

``` bash
#!/bin/bash

# disable-with-heredoc.sh

: <<'DISABLED'
echo "This line will not show up in the console.
echo "Neither will this line.
DISABLED

echo "This line will be printed to the console
```  
By turning a block of code into a heredoc and redirect it to the dummy command ‘:’, we’ve essentially prevented the block of code from executing. We’ve enclosed the delimiter token to escape all the special characters in the content. This is done to prevent any command substitution from being executed, thereby preventing any unintended side effects.

### Here String
- Here string is very similar to a heredoc, except that the former is a much simpler version of heredoc. 
- For that reason, here string does not need a delimiter token. 
- It is usually preferred whenever we need a quick way to redirect some strings into a command.

#### Syntax
To construct a here string, we use “<<<” operator to redirect a string into a command. Concretely, the syntax is:

  ```bash
  COMMAND <<< $VAR
  ```
What it essentially does is expanding the variable VAR and redirect the output string to the COMMAND.

4.2. Basic Usage
The simplest form of here string comprised of a command, followed by a “<<<” redirector and a string:

cat <<< "This is a string"
`bash`
Besides that, we can redirect a variable that contains a string:

WELCOME_MESSAGE="Welcome to dashboard"
cat <<< $WELCOME_MESSAGE
`bash`
4.3. Here String With Parameter
Similar to a heredoc, we can also parameterize the output of our here string with variables:

cat <<< "Welcome! ${USER}"
`bash`
4.4. Escape Special Characters in Here String
To escape special characters like $, \, and `, we can enclose the string with the single quote instead of the double quote:

cat <<< 'Display special characters: $ ` \'
`bash`
If we enclose the here string with a single quote, variable expansion such as ${USER} will stop working.
