---
id: 75dfxfyagrbmv6osbbqxfav
title: Snippets
desc: ''
updated: 1735373099615
created: 1735327666129
---

### Base Application

- An application called fileinfo that takes one or more paths, and prints information about all files found there.
- By default, the host application will just print information common to any file type... the full path, file type, and size.
- The host application will be delivered as an executable Python package, so that the user can use `python -m <package>` to execute it.
- Our host application will use `logging` for logging, and will provide a `--verbose/-v` argument to output verbose logging.

    ```py
    > python -m fileinfo ./test_files

    /my/path/test_files/foo.txt
    .TXT file
    123 bytes

    /my/path/test_files/foo.csv
    .CSV file
    123 bytes
    ```

#### Event

- The host app will expose a single event as it passes through each type.
- Since this is a single event system, instead of some sort of eventing or callback system, the application will manage this event by iterating over the registered plugins and calling the appropriate plugins based on file type.

#### Plugin Discovery

- The host app will discover plugins on its own by checking for top-level modules that
  - begin with fileinfo and
  - end with plugin.
- This is easiest for users, who simply have to install something like `fileinfo-images-plugin` or `fileinfo-text-plugin` to get access to the plugin.
- Plugins
  - will be a callable that
  - is passed a Path object of the file,
  - and will return an iterable of str to print (that signature is `Callable[[Path], Iterable[str]])`.
  - This will keep plugins simple and easy to implement.
- Any exceptions raised by calling a plugin will be logged at debug level and otherwise suppressed.
- All functions that can respond to a file extension will.
- Since we are using logging, plugins can generate log output by creating a Logger instance and logging to it.
- It is recommended that this logging be at debug level, as any logging at info level or above will be output to the user at runtime.

``` py
# src/__main__.py
"""Command line interface."""

# --- START imports and globals ---
import argparse
import logging
import re
from pathlib import Path

from .plugins import FileInfoHandlerFunction, find_all_functions

LOG = logging.getLogger(__name__)
# --- END imports and globals ---


# --- START CLI ---
# Process inputs via cli
def _get_command_line() -> argparse.Namespace:
    parser = argparse.ArgumentParser(description="Get information on files.")

    parser.add_argument(
        "--verbose", "-v", action="store_true", help="Enable verbose output."
    )
    parser.add_argument("path", nargs="+", type=Path, help="Paths to search.")

    return parser.parse_args()
# --- END CLI ---


# --- START Iterate paths ---
def find_all_files(paths: list[Path]) -> list[Path]:
    """Find all of the file paths in a set of paths.

    Args:
        paths: List of paths to include.

    Returns:
        Sorted list of paths.
    """
    file_paths = set() # using set to filter out duplicates
    for path in paths:
        if path.is_file():
            file_paths.add(path)
        else:
            for child in path.rglob("*"):
                if child.is_file():
                    file_paths.add(child)
    return sorted(file_paths) # sorted to ensure files are in a deterministic order


# --- END Iterate paths ---


# --- START File processor ---
def process_file(
    path: Path,
    processor_functions: list[tuple[str, FileInfoHandlerFunction]],
):
    """Process a single file path.

    Args:
        path: Path of the file.
        processor_functions: List of 2-element tuples, containing a file
            extension regular expression, and a corresponding function.
    """
    LOG.debug("--> Processing file %s (%r)", path, path.suffix)
    for pattern, processor in processor_functions:
        if not re.match(pattern, path.suffix):
            continue

        LOG.debug("==> Calling %s", processor)
        try:
            LOG.info("\n".join(map(str, processor(path))))
        except Exception:
            LOG.debug(
                "ERROR running %s on %s",
                processor,
                path,
                exc_info=True,
            )

    LOG.debug("<-- Finished %s", path)
    LOG.info("")


# --- END File processor ---

# --- START main ---
if __name__ == "__main__":
    args = _get_command_line()
    logging.basicConfig(
        level=logging.DEBUG if args.verbose else logging.INFO,
        format="%(message)s",
    )

    processors = find_all_functions()

    for path in find_all_files(args.path):
        process_file(path, processors)
# --- END main ---
```

### Discovery of Plugins

```py
# src/plugin.py
import importlib
import inspect
import logging
import pkgutil
from collections.abc import Callable, Iterable
from pathlib import Path
from typing import Any, TypeVar

# This is the signature of a function that could be decorated.
FileInfoHandlerFunction = Callable[[Path], Iterable[str]]
# This is a TypeVar to indicate that we get out what we put in.
F = TypeVar("F", bound=FileInfoHandlerFunction)

# Step 0: A method to register plugins 
# This allows the plugin developer to control which functions are called for which file types.
# This allows us to "tag" the callable to a specific file type. 
# This also allows us to use the decorator on a function more than once to register it to more than 1 file type pattern.
# --- START Decorator ---
LOG = logging.getLogger(__name__)
ATTR_NAME = "_fileinfo_registered_type"


def file_type(*patterns: str) -> Callable[[F], F]:
    """Decorates a callable to indicate that it handles a certain type.

    Args:
        pattern: Regex patterns of the file extension to match.

    Returns:
        A decorator function that returns the original function, but with
        additional attributes to mark the function.
    """

    def wrapper(func: F) -> F:
        """Wraps a callable to mark it for a given type."""
        LOG.debug("Registering %s to %s", func, patterns)
        if isinstance(
            (registered_types := getattr(func, ATTR_NAME, None)),
            set,
        ):
            # appends to an existing set if the attribute exists and is a set
            LOG.debug("Adding to existing set")
            registered_types.update(patterns)
        else:
            # adds a new _fileinfo_registered_type attribute with a new set of regex patterns
            LOG.debug("Creating new set")
            setattr(func, ATTR_NAME, {*patterns})

        return func

    return wrapper
# --- END Decorator ---

# --- START Predicate ---
# Step 1: Identify a plugin function
# Our decorator works by adding an attribute containing a set of regex patterns for the files that the function supports, # so let's start by creating a predicate function to define the function. 
# Uses ATTR_NAME defined above

def _is_plugin_func(obj: Any) -> bool:
    """Predicate function for finding a plugin registered function.

    Args:
        obj: Object to test.

    Returns:
        True if this is a function with the _fileinfo_registered_types
        attribute, and False otherwise.
    """
    return inspect.isfunction(obj) and hasattr(obj, ATTR_NAME)
# --- END Predicate ---

# --- START Function Finder ---

# Step 2: Load plugin functions from a module given its name
# To load and search a module given its name. 
# Import the module using importlib.import_module and inspect.getmembers to search. 
def _find_functions_in_module(
    module_name: str,
) -> list[tuple[str, FileInfoHandlerFunction]]:
    """Find all of the functions within a given module by name.

    Args:
        module_name: Name of the module to import.

    Returns:
        List of matching functions.
    """
    found_handlers = []
    LOG.debug("Importing %s", module_name)
    try:
        module = importlib.import_module(module_name)
    # Some exception handling around the import, just in case there is bad code in there, so that the host application doesn't crash.
    except Exception:
        LOG.debug("Skipping %s due to import error", module_name)
    else:
        for _, func in inspect.getmembers(module, _is_plugin_func):
            for type_pattern in getattr(func, ATTR_NAME):
                LOG.debug("Adding %r for %s", type_pattern, func)
                found_handlers.append((type_pattern, func))

    return found_handlers


# --- END Function Finder ---


# --- START Plugin Finder ---

# Step 3: Find modules that match our plugin name and search them for plugin functions
# 1. Use pkgutil.iter_modules to iterate through the top level modules, looking for ones that start with fileinfo and end with plugin. 
# 2. Use pkgutil.walk_packages to walk the packages and modules inside, and pass each module we find to our function above to find.

def find_all_functions() -> list[tuple[str, FileInfoHandlerFunction]]:
    """Find all functions that can be registered to a type.

    Returns:
        List of tuples, containing the type patterns and their associated
        functions.
    """
    found_handlers = []
    LOG.debug("--> Finding handler plugins")

    # Add the default behavior
    found_handlers += _find_functions_in_module(__name__)

    # Search all top-level packages/modules
    for _, module_name, is_pkg in pkgutil.iter_modules():
        # If it doesn't start with fileinfo and end with plugin, skip it!
        if not all(
            (
                module_name.startswith("fileinfo"),
                module_name.endswith("plugin"),
            )
        ):
            LOG.debug("Skipping %s", module_name)
            continue

        if is_pkg:
            # If this is a package, walk it, so we can search each submodule
            LOG.debug("Importing %s for submodules", module_name)
            try:
                module = importlib.import_module(module_name)
            except Exception:
                LOG.debug(
                    "Skipping %s due to error", module_name, exc_info=True
                )
            else:
                for _, module_name, _ in pkgutil.walk_packages(module.__path__):
                    found_handlers += _find_functions_in_module(module_name)

        else:
            found_handlers += _find_functions_in_module(module_name)

    LOG.debug("<-- Found %d handler functions", len(found_handlers))
    return found_handlers


# --- END Plugin Finder ---


# --- START Default handler ---

# Assigned to the regular expression ".*" which will match any string, even an empty one. 
# This means that this function will be called for every file.
@file_type(".*")
def default(path: Path) -> Iterable[str]:
    """Default handler for any file type.

    Args:
        path: Path to examine.

    Returns:
        Information on the file as lines of text.
    """
    yield f"{path.resolve()}"
    yield f"{path.suffix.upper()} file" if path.suffix else "File"
    yield f"{path.stat().st_size} bytes"


# --- END Default handler ---

```

### The Plugins

#### CSV Plugin

- The plugin will respond to events for .csv files, and expose the following information:
  - Count the number of columns.
  - Count the number of rows.
  - If there was an error parsing the file as a CSV, and report 0 for the counts.

``` py
# fileinfo_csv_plugin.py
# --- START fileinfo_csv_plugin ---
"""Plugin for fileinfo for CSV files."""

from collections.abc import Iterable
from csv import reader
from pathlib import Path

from fileinfo.plugins import file_type


@file_type(r"\.csv")
def process_csv(path: Path) -> Iterable[str]:
    """Process a CSV file.

    Args:
        path: Path of the file to check.

    Yields:
        Information on columns and rows.
    """
    with path.open(mode="r") as f:
        contents = list(reader(f))

    yield f"Rows {len(contents)}"
    yield f"Columns {max(len(r) for r in contents)}"


# --- END fileinfo_csv_plugin ---
> python -m fileinfo ./test_files

/my/path/test_files/foo.txt
.TXT file
123 bytes

/my/path/test_files/foo.csv
.CSV file
123 bytes
Rows 3
Columns 4
```

#### TEXT plugin

- The plugin will respond to events for .txt files, and expose the following information:
  - Count the number of lines (substrings split by newlines).
  - Count the number of words (non-empty, non-space substrings split by spaces).

``` py
# fileinfo_txt.plugin.py
# --- START fileinfo_text_plugin ---
"""Plugin for fileinfo for TXT files."""

from collections.abc import Iterable
from pathlib import Path

from fileinfo.plugins import file_type


@file_type(r"\.txt")
def process_txt(path: Path) -> Iterable[str]:
    """Process a TXT file.

    Args:
        path: Path of the file to check.

    Yields:
        Information on number of lines and number of words.
    """
    contents = path.read_text()
    yield f"Lines {len(contents.split('\n'))}"
    yield f"Words {len(contents.split())}"


# --- END fileinfo_text_plugin ---

> python -m fileinfo ./test_files

/my/path/test_files/foo.txt
.TXT file
123 bytes
Lines 3
Words 23

/my/path/test_files/foo.csv
.CSV file
123 bytes

```
