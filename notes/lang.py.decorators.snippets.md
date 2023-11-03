---
id: 6ssa2mtvd7zzjphgm55qsym
title: snippets
desc: ''
updated: 1697304071318
created: 1697197931701
---


## Template Code

```py
def ______(maxsize: int = 128, typed: bool = False, ttl: int = -1):
    def wrapper(func: Callable) -> Callable:
        def wrapped(*args, **kwargs) -> Any:
            return func(*args, **kwargs)
        return update_wrapper(wrapped, func)
    return wrapper
```
## Timer

```py
import time

def timer(func):
    def wrapper(*args, **kwargs):
        # start the timer
        start_time = time.time()
        # call the decorated function
        result = func(*args, **kwargs)
        # remeasure the time
        end_time = time.time()
        # compute the elapsed time and print it
        execution_time = end_time - start_time
        print(f"Execution time: {execution_time} seconds")
        # return the result of the decorated function execution
        return result
    # return reference to the wrapper function
    return wrapper
```

## Debugger

```py
import logging
logging.basicConfig(level=logging.DEBUG)

def debug(func):
    '''        
    @debug
    def f(a,b,c=0):
        raise Exception("Crashing")
        return a+b+c
    f(1,2,c=3)
    
    Output:
    DEBUG:root:f called with args(1, 2) kwargs{'c': 3}
    DEBUG:root:f **EXCEPTION** Crashing
    DEBUG:root:f returned [None]
    '''    
    log = logging.getLogger()
    def wrapper(*args, **kwargs):
        log.debug(f"{func.__qualname__} called with args{args} kwargs{kwargs}")
        result = None
        try:
            result = func(*args, **kwargs)
        except Exception as e:
            log.debug(f"{func.__qualname__} **EXCEPTION** {e}")
        finally:
            log.debug(f"{func.__qualname__} returned [{result}]")
        return result
    return wrapper    
```

## Retry

``` py
import time

def retry(max_attempts, delay=1):
    def decorator(func):
        def wrapper(*args, **kwargs):
            attempts = 0
            while attempts < max_attempts:
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    attempts += 1
                    print(f"Attempt {attempts} failed: {e}")
                    time.sleep(delay)
            print(f"Function failed after {max_attempts} attempts")
        return wrapper
    return decorator
```