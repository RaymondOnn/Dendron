---
id: qt77xg1ilml2fcdqdm3xuto
title: Suppress
desc: ''
updated: 1697836962651
created: 1697836089762
---


### Suppressing Exception

- The pass keyword is a placeholder statement in Python that does nothing.
- Useful when you want to catch an exception and handle it later or when you want to ignore the exception entirely.

### A better way

- `contextlib.suppress` is a context manager in Python that allows you to suppress specific exceptions from being raised within a block of code.  
- `contextlib.suppress` helps you write cleaner and more concise error handling code when you don't need to do anything if a particular exception occurs.
- Note: `contextlib.suppress` is slightly slower and therefore, should be avoided when dealing with very high performant systems. Lag becomes significant at high iterations

  ``` py
    import contextlib
    import os
    import timeit

    filename = "nonexistent_file.txt"

    def try_except_method():
        try:
            os.remove(filename)
        except FileNotFoundError:
            pass

    def suppress_method():
        with contextlib.suppress(FileNotFoundError):
            os.remove(filename)

    # Measure the time it takes to execute each method
    try_except_time = timeit.timeit(try_except_method, number=1_000)
    suppress_time = timeit.timeit(suppress_method, number=1_000)

    print(f"try-except time: {try_except_time:.6f} seconds")
    print(f"contextlib.suppress time: {suppress_time:.6f} seconds")
  ```
  <br>
  
  **Results**

  ``` raw
  Result for 10 iterations:
  try-except time: 0.000045 seconds
  contextlib.suppress time: 0.000049 seconds

  Result for 10.000.000 iterations:
  try-except time: 20.464040 seconds
  contextlib.suppress time: 28.150847 seconds
  ```

Compare both versions of code

``` py
import os
import contextlib

filename = "example.txt"

# Without contextlib.suppress
try:
    os.remove(filename)
except FileNotFoundError:
    pass

# With contextlib.suppress
with contextlib.suppress(FileNotFoundError):
    os.remove(filename)
```

- Both versions of the code above achieve the same result:
  - We try to remove a file and do nothing if the file is not found.
  - The version using contextlib.suppress is more concise and arguably more readable.

### An real world example: Deleting Temp Files

``` py
import os
import contextlib
import logging

def clean_temp_files(temp_directory, extensions_to_remove):
    """Remove temporary files with specific extensions from a directory."""
    # walk through directory
    for root, _, files in os.walk(temp_directory):
        for file in files:
            # check for matching extensions
            if any(file.endswith(ext) for ext in extensions_to_remove):
                file_path = os.path.join(root, file)
                # ignore error if file not found or if not allowed to delete
                with contextlib.suppress(FileNotFoundError, PermissionError):
                    # delete file
                    os.remove(file_path)
                    logging.info(f"Deleted temporary file: {file_path}")

temp_directory = "/tmp"
extensions_to_remove = [".tmp", ".log"]

clean_temp_files(temp_directory, extensions_to_remove)
```
