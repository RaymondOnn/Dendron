---
id: 3wuw6656277peti9xxhmg88
title: Error_handling
desc: ''
updated: 1692038514002
created: 1691997473790
---


## The `try-except` block

link: https://www.youtube.com/watch?v=ZUqGMDppEDs&ab_channel=NeuralNine

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


## Monadic Error Handling

### Monads: Separate business logic from the error-handling logic

### Railway Oriented Programming



- Coming from the functional programming paradigm
- Instead of using exception, the function can instead return either of two values
  - 'Success' object, containing the result of the computation
  - 'Failure' object, containing the error that occurred
- When chaining functions, each function should then
  - output either 'Success' or 'Failure'
  - accept 'Success' or 'Failure' as inputs
- In essence, there will be two tracks, a 'Success' track and a 'Failure' track flow through the program
  - If everything goes well, you stay on the 'Success' track
  - But once error occurred, you shift to the 'Failure' track

#### bind: Adapting single track switches to two tracks

```py
def bind(fx):
    def adapt(status_object):
        if isinstance(status, 'Success'):
            return fx(status.value)    # Success / Failure are objects
        else: 
            return status_object  # Failure
        return adapt

update_db_after = bind(update_db_before)
```

#### map: Making single track-function two tracks

```py
def map(fx):
    def adapt(status_object):
        if isinstance(status, Success):
            # note this is compose(fx, Success)
            return Success(fx(status_object.value))    
        else: 
            return status_object  # Failure
        return adapt

# after refactoring
def map(fx):
    return bind(compose(fx, Success))       
```

#### Implementation Example

```py
# package that supports railway programming
# docs: https://github.com/dry-python/returns
pip install returns
```

<br>

```py
import sqlite3
from returns.result import Result, safe
from returns.pipeline import flow
from returns.pointfree import bind

class SQLite():
    def __init__(self, file='application.db'):
        self.file = file
        self.conn = None
    def __enter__(self):
        self.conn = sqlite3.connect(self.file)
        return self.conn.cursor()
    def __exit__(self, type, value, traceback):
        if self.conn: self.conn.close()
    
class NotFoundError(Exception):
    pass

class NotAuthorizedError(Exception):
    pass

def fetch_blog(blog_id) -> Result['Blog', Exception]:
    return flow(
        blog_id,
        fetch_blog_from_db,
        bind(blog_to_dict),
        bind(verify_access)
    )

@safe     # converts exception into either Success / Failure
def fetch_blog_from_db(blog_id):
    """Fetches blog from SQLite3 database."""
    with SQLite('application.db') as cur:
        cur.execute(f"SELECT * FROM blogs where id=?", [blog_id])
        result = cur.fetchone()
        if result is None:
            raise NotFoundError(f'Unable to find blog with id {blog_id}.')
        return result

@safe
def blog_to_dict(item) -> 'Blog':
    """Convert SQLite result to dictionary."""
    return { 
        'id': item[0],
        'published': item[1],
        'title': item[2],
        'content': item[3],
        'public': bool(item[4])
         }

@safe
def verify_access(blog) -> 'Blog':
    """Check that blog is accessible."""
    blog_id = blog['id']
    blog_public = blog['public']
    if not blog_public:
        raise NotAuthorizedError(f'You are not allowed to access blog with id {blog_id}.')
    return blog

res = fetch_blog("first-blog")
print(res)
```

#### Other Resources
- talk on railway programming: https://www.youtube.com/watch?v=cKixdve3JGg&ab_channel=PyData
- Medium article: https://levelup.gitconnected.com/python-the-unacceptable-except-fd633c85c3ae

## Exception Logging Decorator

```py
import logging 
from functools import wraps

# Example from: https://www.geeksforgeeks.org/create-an-exception-logging-decorator-in-python/

def create_logger(): 
	
	# create a logger object 
	logger = logging.getLogger('exc_logger') 
	logger.setLevel(logging.INFO) 
	
	# create a file to store all the 
	# logged exceptions 
	logfile = logging.FileHandler('exc_logger.log') 
	
	fmt = '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
	formatter = logging.Formatter(fmt) 
	
	logfile.setFormatter(formatter) 
	logger.addHandler(logfile) 
	
	return logger 

logger = create_logger() 

# you will find a log file 
# created in a given path 
print(logger) 

def exception(logger):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            try:
                return func(*args, **kwargs)
            except:
                issue = "exception in "+func.__name__+"\n"
                issue = issue+"=============\n"
                logger.exception(issue)
                raise
        return wrapper
    return decorator 


@exception(logger) 
def divideByZero(): 
    return 12/0

# Driver Code 
if __name__ == '__main__': 
    divideByZero() 

```

## Retry Decorator

```py
import time
import math
from functools import wraps

def retry(ExceptionToCheck, tries=4, delay=3, backoff=2, logger=None):
    """Retry calling the decorated function using an exponential backoff.

    http://www.saltycrane.com/blog/2009/11/trying-out-retry-decorator-python/
    original from: http://wiki.python.org/moin/PythonDecoratorLibrary#Retry

    :param ExceptionToCheck: the exception to check. may be a tuple of
        exceptions to check
    :type ExceptionToCheck: Exception or tuple
    :param tries: number of times to try (not retry) before giving up
    :type tries: int
    :param delay: initial delay between retries in seconds
    :type delay: int
    :param backoff: backoff multiplier e.g. value of 2 will double the delay
        each retry
    :type backoff: int
    :param logger: logger to use. If None, print
    :type logger: logging.Logger instance
    """
    def deco_retry(f):

        @wraps(f)
        def f_retry(*args, **kwargs):
            mtries, mdelay = tries, delay
            while mtries > 1:
                try:
                    return f(*args, **kwargs)
                except ExceptionToCheck as e:
                    msg = "%s, Retrying in %d seconds..." % (str(e), mdelay)
                    if logger:
                        logger.warning(msg)
                    else:
                        print(msg)
                    time.sleep(mdelay)
                    mtries -= 1
                    mdelay *= backoff
            return f(*args, **kwargs)

        return f_retry  # true decorator

    return deco_retry

@retry(Exception, tries=4)
def test_fail(text):
    raise Exception("Fail")

test_fail("it works!")
```
