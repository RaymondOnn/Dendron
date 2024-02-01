---
id: mlzian0ayu0qzng6xt1kk5w
title: py
desc: ''
updated: 1706576246311
created: 1666472517896
---
https://python.plainenglish.io/wanna-code-like-a-google-engineer-lets-dive-into-advanced-python-together-93f1e08b6d2f
https://python.plainenglish.io/python-hidden-treasures-delve-deeper-like-a-google-pro-part-2-of-wanna-code-like-a-google-c38069316ee7

For doing designs in printout:
<https://www.lihaoyi.com/post/BuildyourownCommandLinewithANSIescapecodes.html>

<https://www.youtube.com/shorts/fNMgJaDYxvk>
 - [print](https://www.youtube.com/watch?v=Ap5Gx0n24w0)
- [Formatting](https://www.youtube.com/watch?v=FrvBwdAU2dQ&ab_channel=BroCode)
- ColorRama

```py
# format specifiers = {:flags} format a value based on what flags are inserted

# .(number)f = round to that many decimal places
# :(number) = allocate that many spaces
# :0(number) = allocate and zero pad that many spaces
# :< = left justify
# :> = right justify
# :^ = center align
# :+ = use a plus sign to indicate positive value
# := = place sign to leftmost position
# :  = insert a space before positive numbers
# :, = comma separator
# :% = percentage format

price1 = 3.14159
price2 = -987.65
price3 = 12.34
print(f"price1 is: ${price1:}")
print(f"price2 is: ${price2:}")
print(f"price3 is: ${price3:}")
```


### Command Line Options
- `-B`: Don't write `.py[co]` files on import. See also `PYTHONDONTWRITEBYTECODE`.
- `-c command`: Specify the command to execute (see next section). This terminates the option list (following options are passed as arguments to the command).
-d
Turn on parser debugging output (for wizards only, depending on compilation options).
-E
Ignore environment variables like PYTHONPATH and PYTHONHOME that modify the behavior of the interpreter.
- `-h`, `-?`, `--help`: Prints the usage for the interpreter executable and exits.
-i
When a script is passed as first argument or the -c option is used, enter interactive mode after executing the script or the command. It does not read the `$PYTHONSTARTUP` file. This can be useful to inspect global variables or a stack trace when a script raises an exception.
- `-m module-name`: Searches `sys.path` for the named module and runs the corresponding `.py` file as a script.
- `-O`: Turn on basic optimizations. This changes the filename extension for compiled (bytecode) files from `.pyc` to `.pyo`. Given twice, causes docstrings to be discarded.
- `-O0`: Discard docstrings in addition to the `-O` optimizations.
-R
Turn on "hash randomization", so that the hash() values of str, unicode, buffer and datetime objects are "salted" with an unpredictable pseudo-random value. Although they remain constant within an individual Python process, they are not predictable between repeated invocations of Python.
This is intended to provide protection against a denial of service
caused by carefully-chosen inputs that exploit the worst case performance of a dict construction, O(n^2) complexity. See http://www.ocert.org/advisories/ocert-2011-003.html for details.
-Q argument
Division control; see PEP 238. The argument must be one of "old" (the default, int/int and long/long return an int or long), "new" (new division semantics, i.e. int/int and long/long returns a float), "warn" (old division semantics with a warning for int/int and long/long), or "warnall" (old division semantics with a warning for all use of the division operator). For a use of "warnall", see the Tools/scripts/fixdiv.py script.
-s
Don't add user site directory to sys.path.
-S
Disable the import of the module site and the site-dependent manipulations of sys.path that it entails.
-t
Issue a warning when a source file mixes tabs and spaces for indentation in a way that makes it depend on the worth of a tab expressed in spaces. Issue an error when the option is given twice.
-u
Force stdin, stdout and stderr to be totally unbuffered. On systems where it matters, also put stdin, stdout and stderr in binary mode. Note that there is internal buffering in xreadlines(), readlines() and file-object iterators ("for line in sys.stdin") which is not influenced by this option. To work around this, you will want to use "sys.stdin.readline()" inside a "while 1:" loop.
-v
Print a message each time a module is initialized, showing the place (filename or built-in module) from which it is loaded. When given twice, print a message for each file that is checked for when searching for a module. Also provides information on module cleanup at exit.
-V ", " --version
Prints the Python version number of the executable and exits.
-W argument
Warning control. Python sometimes prints warning message to sys.stderr. A typical warning message has the following form: file:line: category: message. By default, each warning is printed once for each source line where it occurs. This option controls how often warnings are printed. Multiple -W options may be given; when a warning matches more than one option, the action for the last matching option is performed. Invalid -W options are ignored (a warning message is printed about invalid options when the first warning is issued). Warnings can also be controlled from within a Python program using the warnings module.
The simplest form of argument is one of the following action strings (or a unique abbreviation): ignore to ignore all warnings; default to explicitly request the default behavior (printing each warning once per source line); all to print a warning each time it occurs (this may generate many messages if a warning is triggered repeatedly for the same source line, such as inside a loop); module to print each warning only the first time it occurs in each module; once to print each warning only the first time it occurs in the program; or error to raise an exception instead of printing a warning message.

The full form of argument is action:message:category:module:line. Here, action is as explained above but only applies to messages that match the remaining fields. Empty fields match all values; trailing empty fields may be omitted. The message field matches the start of the warning message printed; this match is case-insensitive. The category field matches the warning category. This must be a class name; the match test whether the actual warning category of the message is a subclass of the specified warning category. The full class name must be given. The module field matches the (fully-qualified) module name; this match is case-sensitive. The line field matches the line number, where zero matches all line numbers and is thus equivalent to an omitted line number.

-x
Skip the first line of the source. This is intended for a DOS specific hack only. Warning: the line numbers in error messages will be off by one!
-3
Warn about Python 3.x incompatibilities that 2to3 cannot trivially fix.