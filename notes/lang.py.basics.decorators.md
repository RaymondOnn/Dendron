---
id: 3wkiqhqg7cw748xx3i2z1sr
title: decorators
desc: ''
updated: 1697197912157
created: 1693821529395
---

## Decorators

- Takes an object and adds some behaviour to it
- Great solution for cross-cutting concerns
  - E.g. Logging, Authentication, Benchmarking, User Tracking

``` py
# Eapping a function with another function

def is_prime(number):
    ...

def count_prime_number(upper_bound: int) -> int:
    count = 0
    for number in range(upper_bound):
        if is_prime(number):
            count += 1
    return count

def benchmark(upper_bound:int) -> int:
    start_time = time.perf_counter()
    value = count_prime_numbers(upper_bound)
    end_time = time.perf_counter()
    run_time = end_time - start_time
    logging.info(
        f"Execution of count_prime_numbers took {run_time:.2f} seconds"
    )
    return value

def main() -> None:
    logging.basicConfig(level=logging.INFO)
    value = benchmark(100000)
    logging,info(f"Number of primes: {value}.")

if __name__ = "__main__":
    main()    
```

## Generalizing the decorator function

``` py
# Eapping a function with another function

def is_prime(number):
    ...

def count_prime_number(upper_bound: int) -> int:
    count = 0
    for number in range(upper_bound):
        if is_prime(number):
            count += 1
    return count

def benchmark(func:Callable[..., Any]) -> Callable[..., Any]:
    def wrapper(*args: Any, **kwargs:Any) -> Any:
        start_time = time.perf_counter()
        value = func(*args, **kwargs)
        end_time = time.perf_counter()
        run_time = end_time - start_time
        logging.info(
            f"Execution of {func.__name__} took {run_time:.2f} seconds"
        )
        return value
    return wrapper

def main() -> None:
    logging.basicConfig(level=logging.INFO)
    wrapper = benchmark(count_prime_numbers)
    value = wrapper(100000)
    logging,info(f"Number of primes: {value}.")

if __name__ = "__main__":
    main()    
```

## Using the '@' operator

Syntactic sugar for decorators

``` py
import functools

def is_prime(number):
    ...

def benchmark(func:Callable[..., Any]) -> Callable[..., Any]:
    @functools.wraps(func)     # to fix func.__name__ = wrapper.__name__ issue
    def wrapper(*args: Any, **kwargs:Any) -> Any:
        start_time = time.perf_counter()
        value = func(*args, **kwargs)
        end_time = time.perf_counter()
        run_time = end_time - start_time
        logging.info(
            f"Execution of {func.__name__} took {run_time:.2f} seconds"
        )
        return value
    return wrapper

@benchmark
def count_prime_number(upper_bound: int) -> int:
    count = 0
    for number in range(upper_bound):
        if is_prime(number):
            count += 1
    return count

def main() -> None:
    logging.basicConfig(level=logging.INFO)
    value = count_prime_number(100000)
    logging,info(f"Number of primes: {value}.")

if __name__ = "__main__":
    main()    
```

## Adding arguments to decorator

``` py
# before
def with_logging(func:Callable[..., Any]) -> Callable[..., Any]:
    @functools.wraps(func)     # to fix func.__name__ = wrapper.__name__ issue
    def wrapper(*args: Any, **kwargs:Any) -> Any:
        logging.info(f"Calling {func.__name__}")
        value = func(*args, **kwargs)
        logging.info(
            f"Finished calling {func.__name__}")
        return value
    return wrapper

# decorator now has arguments
@with_logging
def func(...):
    ... 

# after
logger = logging.getLogger("my_app")

def with_logging(logger:logging.Logger)
    def decoratpr(func:Callable[..., Any]) -> Callable[..., Any]:
        @functools.wraps(func)     # to fix func.__name__ = wrapper.__name__ issue
        def wrapper(*args: Any, **kwargs:Any) -> Any:
            logger.info(f"Calling {func.__name__}")
            value = func(*args, **kwargs)
            logger.info(
                f"Finished calling {func.__name__}")
            return value
        return wrapper
    return decorator

# decorator now has arguments
@with_logging(logger=logger)
def func(...):
    ...    

```

## Decorator defaults using `functools.partial`

To avoid having to provide the same arguments throughout, we can create a default version of the decorator instead.

``` py
logger = logging.getLogger("my_app")


def with_logging(func:Callable[..., Any], logger:logging.Logger) -> Callable[..., Any]:
    @functools.wraps(func)     # to fix func.__name__ = wrapper.__name__ issue
    def wrapper(*args: Any, **kwargs:Any) -> Any:
        logger.info(f"Calling {func.__name__}")
        value = func(*args, **kwargs)
        logger.info(
            f"Finished calling {func.__name__}")
        return value
    return wrapper


with_default_logging = functools.partial(with_logging, logger=logger)

# decorator now has arguments
@with_default_logging
def func(...):
    ...  
```

### Issues regarding decorators

- Your code might become harder to read. Things start getting complex when multiple decorators get used
- Decorators might modify a function's signature i.e. function returns output of the different data type that orignally expected
