---
id: abwui9mg4f5akyc7f9vpvnf
title: Basics
desc: ''
updated: 1693848791338
created: 1693805646696
---
- [Python Exception Docs](https://docs.python.org/3/library/exceptions.html)
## The `try-except` block

link: https://www.youtube.com/watch?v=ZUqGMDppEDs&ab_channel=NeuralNine

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

## Custom Exceptions: Creating better error messages

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