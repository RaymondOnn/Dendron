---
id: hnrez4zzbrzlv9v7erh9s9r
title: hooks
desc: ''
updated: 1728233006080
created: 1728232461821
---
<https://python.plainenglish.io/creating-beautiful-tracebacks-with-pythons-exception-hooks-c8a79e13558d>
<https://sentry.io/answers/print-stack-traces-in-python/>

### Exception Hooks

- Whenever an exception is raised and isn’t handled by `try/except` block, a function assigned to sys.excepthook is called.
- This function - called Exception Hook - is then used to output any relevant information to standard output using the 3 arguments it receives: `type`, `value` and `traceback`.

```py
import sys

def exception_hook(exc_type, exc_value, tb):
    print('Traceback:')
    filename = tb.tb_frame.f_code.co_filename
    name = tb.tb_frame.f_code.co_name
    line_no = tb.tb_lineno
    print(f"File {filename} line {line_no}, in {name}")

    # Exception type and value
    print(f"{exc_type.__name__}, Message: {exc_value}")

sys.excepthook = exception_hook


def do_stuff():
    # ... do something that raises exception
    raise ValueError("Some error message")

do_stuff()

# Traceback:
# File /home/some/path/exception_hooks.py line 22, in <module>
# ValueError, Message: Some error message


# A version that provides a better picture of the call stack
def exception_hook(exc_type, exc_value, tb):

    local_vars = {}
    while tb:
        filename = tb.tb_frame.f_code.co_filename
        name = tb.tb_frame.f_code.co_name
        line_no = tb.tb_lineno
        print(f"File {filename} line {line_no}, in {name}")

        local_vars = tb.tb_frame.f_locals
        tb = tb.tb_next
    print(f"Local variables in top frame: {local_vars}")

...

# File /home/some/path/exception_hooks.py line 41, in <module>
# File /home/some/path/exception_hooks.py line 7, in do_stuff
# Local variables in top frame: {'some_var': 'data'}
```
