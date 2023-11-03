---
id: abwui9mg4f5akyc7f9vpvnf
title: Basics
desc: ''
updated: 1697279250509
created: 1693805646696
---
- [Python Exception Docs](https://docs.python.org/3/library/exceptions.html)

### The `try-except` block

link: <https://www.youtube.com/watch?v=ZUqGMDppEDs&ab_channel=NeuralNine>

Easier to ask forgiveness than permission

```py
    import logging
    
    values = [10, 5, 6, 0, 3, 7, 4]

    for value in values:
        try:
            print(10 / value)
        except ValueError as e:
            print(str(e))
            raise
        except ZeroDivisionError as e:    
            # pass: do nothing
            # continue: 
        except Exception as e:
            logging.exception(e)
        else:
            pass    
        finally:
            pass
            
```

### Custom Exceptions: Creating better error messages

```py
# create new Exception
# Note: base class: Exception class
class NameTooShortError(ValueError):
    pass

def validate(name):
    if len(name) < 10
    # raise ValueError
    # raise ValueError('NameTooShortError)
    # raise NameTooShortError
    raise NameTooShortError(Name)
```

### Exception Handling Best Practices

#### Specificity and Granularity in Exception Handling

Always handle exceptions as specifically as possible. Catching all exceptions is generally a bad idea because it can hide bugs and make debugging difficult.

#### Avoiding Broad Except Clauses

Instead of catching all exceptions, aim to catch specific exceptions that you expect might occur during the execution of your code.

#### Logging and Reporting Errors

Always log errors for diagnostic purposes. You can use Python’s built-in `logging` module to record when exceptions occur.

#### Graceful Degradation and Graceful Exit

Your program should always fail gracefully, informing the user about what happened and, if possible, continuing to work despite the error. If not, it should clean up any resources it was using and exit without causing any further damage.

### Advanced Error Handling Techniq
