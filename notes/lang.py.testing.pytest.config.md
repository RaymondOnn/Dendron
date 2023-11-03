---
id: f9u8kgwqrl9mm8n3er4hgtt
title: config
desc: ''
updated: 1698634000575
created: 1698629593610
---
- [Configuration](#configuration)
  - [Configuration Files](#configuration-files)
  - [Configuration Settings](#configuration-settings)
  - [Changing the Default Command-Line Options](#changing-the-default-command-line-options)
  - [Registering Markers to Avoid Marker Typos](#registering-markers-to-avoid-marker-typos)
  - [Requiring a Minimum pytest Version](#requiring-a-minimum-pytest-version)
  - [Stopping pytest from Looking in the Wrong Places](#stopping-pytest-from-looking-in-the-wrong-places)
  - [Specifying Test Directory Locations](#specifying-test-directory-locations)
  - [Changing Test Discovery Rules](#changing-test-discovery-rules)
    - [python\_classes](#python_classes)
    - [python\_files](#python_files)
    - [python\_functions](#python_functions)
  - [Disallowing XPASS](#disallowing-xpass)
  - [Avoiding Filename Collisions](#avoiding-filename-collisions)

## Configuration

### Configuration Files

- `pytest.ini`:  Primary pytest configuration file that allows you to change default behavior.
- `conftest.py`: A local plugin to allow hook functions and fixtures for the directory where the conftest.py file exists and all subdirectories.
- `__init__.py`: When put into every test subdirectory, this file allows you to have identical test filenames in multiple test directories.  See Avoiding Filename Collisions​.

### Configuration Settings

- You can get a list of all the valid settings for `pytest.ini` from `pytest — help`

``` sh
 ​$ ​​pytest​​ ​​--help​
​ ​...​
​ [pytest] ini-options in the first pytest.ini|tox.ini|setup.cfg file found:
​ 
​   markers (linelist)       markers for test functions
​   norecursedirs (args)     directory patterns to avoid for recursion
​   testpaths (args)         directories to search for tests when no files or
​                            directories are given in the command line.
​   usefixtures (args)       list of default fixtures to be used with this project
​   python_files (args)      glob-style file patterns for Python test module discovery
​   python_classes (args)    prefixes or glob names for Python test class discovery
​   python_functions (args)  prefixes or glob names for Python test function and
​                            method discovery
​   xfail_strict (bool)      default for the strict parameter of xfail markers
​                            when not given explicitly (default: False)
​   doctest_optionflags (args) option flags for doctests
​   addopts (args)           extra command line options
​   minversion (string)      minimally required pytest version
​ ​...​
```

- It is possible for plugins (and conftest.py files) to add ini file options. The added options will be added to the `pytest — help` output as well.

### Changing the Default Command-Line Options

You’ve used a lot of command-line options for pytest so far, like -v/ — verbose for verbose output and -l/ — showlocals to see local variables with the stack trace for failed tests. You may find yourself always using some of those options — or preferring to use them — for a project.

- For frequently used command-line options, you can set addopts in pytest.ini to the options you want and won't have to type them in anymore.
- Here’s a set I like:

``` raw
​ ​[pytest]​
​ addopts = ​-rsxX -l --tb=short --strict​
```

- The -rsxX tells pytest to report the reasons for all tests that skipped, xfailed, or xpassed.
- The -l tells pytest to report the local variables for every failure with the stacktrace.
- The — tb=short removes a lot of the stack trace. It leaves the file and line number, though.
- The — strict option disallows markers to be used if they aren’t registered in a config file.

### Registering Markers to Avoid Marker Typos

- Custom  [[lang.py.testing.pytest.marks]] are great for allowing you to mark a subset of tests to run with a specific marker.
- However, it’s too easy to misspell a marker and end up having some tests marked with `@pytest.mark.smoke` and some marked with `@pytest.mark.somke`.
- By default, this isn’t an error. pytest just thinks you created two markers.
- This can be fixed by registering markers in `pytest.ini`

``` raw
​ ​[pytest]​
​ markers =
​   ​smoke:​ ​Run​ ​the​ ​smoke​ ​test​ ​functions​ ​for​ ​tasks​ ​project​
​   ​get:​ ​Run​ ​the​ ​test​ ​functions​ ​that​ ​test​ ​tasks.get()​
```

- With these markers registered, you can now also see them with pytest — markers with their descriptions:

    ``` sh
    ​ ​$ ​​cd​​ ​​/path/to/code/ch6/b/tasks_proj/tests​
    ​ ​$ ​​pytest​​ ​​--markers​
    ​ @pytest.mark.smoke: Run the smoke test test functions
    ​ 
    ​ @pytest.mark.get: Run the test functions that test tasks.get()
    ​ 
    ​ ​...​
    ​ 
    ​ @pytest.mark.skip(reason=None): skip the ...
    ​ 
    ​ ​...​
    ```

- if you use `—-strict`, any misspelled or unregistered markers show up as an error.

    ``` sh
    # Output when --strict option used + not registering markers
    
    ​ ​$ ​​cd​​ ​​/path/to/code/ch6/a/tasks_proj/tests​
    ​ ​$ ​​pytest​​ ​​--strict​​  ​​--tb=line​
    ​ =================== test session starts ===================
    ​ plugins: cov-2.5.1
    ​ collected 45 items / 2 errors
    ​ 
    ​ ========================= ERRORS ==========================
    ​ ____________ ERROR collecting func/test_add.py ____________
    ​ func/test_add.py:20: in <module>
    ​     @pytest.mark.smoke
    ​ ​...​
    ​ E   AttributeError: 'smoke' not a registered marker
    ​ ______ ERROR collecting func/test_api_exceptions.py _______
    ​ func/test_api_exceptions.py:30: in <module>
    ​     @pytest.mark.smoke
    ​ ​...​
    ​ E   AttributeError: 'smoke' not a registered marker
    ​ !!!!!!!!! Interrupted: 2 errors during collection !!!!!!!!!
    ​ ================= 2 error in 0.29 seconds =================
    ```

- Hence if you use markers in pytest.ini to register your markers, it's recommended to add `— strict`` to the`addopts`.

``` raw
ch6/b/tasks_proj/tests/pytest.ini

​ ​[pytest]​
​ addopts = ​-rsxX -l --tb=short --strict​
​ markers =
​   ​smoke:​ ​Run​ ​the​ ​smoke​ ​test​ ​test​ ​functions​
​   ​get:​ ​Run​ ​the​ ​test​ ​functions​ ​that​ ​test​ ​tasks.get()​
```

- This has a combination of flags I prefer over the defaults:
  - `-rsxX` to report which tests skipped, xfailed, or xpassed,
  - `— tb=short` for a shorter traceback for failures, and
  - `— strict` to only allow declared markers.
  - a list of markers to allow for the project.

This should allow us to run tests, including the smoke tests:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch6/b/tasks_proj/tests​
​ ​$ ​​pytest​​ ​​--strict​​ ​​-m​​ ​​smoke​
​ =================== test session starts ===================
​ plugins: cov-2.5.1
​ collected 57 items / 54 deselected
​ 
​ func/test_add.py .                                  [ 33%]
​ func/test_api_exceptions.py ..                      [100%]
​ 
​ ========= 3 passed, 54 deselected in 0.13 seconds =========
```

### Requiring a Minimum pytest Version

- The minversion setting enables you to specify a minimum pytest version you expect for your tests.
- For e.g. Using `approx()` when testing floating point numbers for “close enough” equality in tests. But this feature didn’t get introduced into pytest until version 3.0.
- To avoid confusion, I add the following to projects that use `approx()`:

    ``` raw
    ​ ​[pytest]​
    ​ minversion = ​3.0​
    ```

- This way, if someone tries to run the tests using an older version of pytest, an error message appears.

### Stopping pytest from Looking in the Wrong Places

- In the case of pytest, test discovery traverses many directories recursively. But there are some directories you just know you don’t want pytest looking in.
- The default setting for norecurse is `.* build dist CVS _darcs {arch} and *.egg`.
- Having ’.*’ is a good reason to name your virtual environment ’.venv’, because all directories starting with a dot will not be traversed.
- However, I have a habit of naming it venv, so I could add that to norecursedirs.

``` raw
​ ​[pytest]​
​ norecursedirs = ​.* venv src *.egg dist build​
```

- When overriding a setting that already has a useful value, like this setting, it’s a good idea to know what the defaults are and put the ones back you care about, as I did in the previous code with `*.egg dist build`.

### Specifying Test Directory Locations

- testpaths tells pytest where to look.
- testspaths is a list of directories relative to the root directory to look in for tests.
- It’s only used if a directory, file, or nodeid is not given as an argument.
- for e.g. for the Tasks project we put `pytest.ini` in the tasks_proj directory instead of under tests.

``` md
​ tasks_proj/
​ ├── pytest.ini
​ ├── src
​ │   └── tasks
​ │       ├── api.py
​ │       └── ...
​ └── tests
​     ├── conftest.py
​     ├── func
​     │   ├── __init__.py
​     │   ├── test_add.py
​     │   ├── ...
​     └── unit
​         ├── __init__.py
​         ├── test_task.py
​         └── ...
```

It could then make sense to put tests in testpaths:

``` raw
​ ​[pytest]​
​ testpaths = ​tests​
```

- Now, as long as you start pytest from the tasks_proj directory, pytest will only look in tasks_proj/tests.
  - My problem with this is that I often bounce around a test directory during test development and debugging, so I can easily test a subdirectory or file without typing out the whole path.
  - Therefore, for me, this setting doesn’t help much with interactive testing.

- However, it’s great for tests launched from a continuous integration server or from tox.
  - In those cases, you know that the root directory is going to be fixed, and you can list directories relative to that fixed root.
  - These are also the cases where you really want to squeeze your test times, so shaving a bit off of test discovery is awesome.

- At first glance, it might seem silly to use both testpaths and norecursedirs at the same time.
- However, as you’ve seen, testspaths doesn’t help much with interactive testing from different parts of the file system.
- In those cases, norecursedirs can help.
- Also, if you have directories with tests that don’t contain tests, you could use norecursedirs to avoid those.

### Changing Test Discovery Rules

- pytest finds tests to run based on certain test discovery rules. - The standard test discovery rules are:
  - Start at one or more directory. You can specify filenames or directory names on the command line. If you don’t specify anything, the current directory is used.
  - Look in the directory and all subdirectories recursively for test modules.
  - A test module is a file with a name that looks like test_*.py or*_test.py.
  - Look in test modules for functions that start with test_.
  - Look for classes that start with Test. Look for methods in those classes that start with test_ but don’t have an __init__ method.

#### python_classes

- The usual test discovery rule for pytest and classes is to consider a class a potential test class if it starts with Test*.
- The class also can’t have an __init__() function.
- python_classes comes in when we want to name our test classes `<something>Test` or `<something>Suite`

``` raw
​ ​[pytest]​
​ python_classes = ​*Test Test* *Suite​
```

This enables us to name classes like this:

``` py
​ ​class​ DeleteSuite():
​   ​def​ ​test_delete_1​():
​      ...
​ 
​   ​def​ ​test_delete_2​():
​      ...
​ 
​   ....
```

#### python_files

- python_files modifies the default test discovery rule, which is to look for files that start with test_*or end in*_test.

- Let’s say you have a custom test framework in which you named all of your test files `check_<something>.py`, just add a line to pytest.ini like this:

``` raw
​ ​[pytest]​
​ python_files = ​test_* *_test check_*​
```

#### python_functions

- python_functions acts similarly. but for test function and method names.
- The default is test_*.
- To add check_*, do this:

``` raw
​ ​[pytest]​
​ python_functions = ​test_* check_*​
```

### Disallowing XPASS

- Setting xfail_strict = true causes tests marked with @pytest.mark.xfail that don’t fail to be reported as an error. I think this should always be set.

### Avoiding Filename Collisions

- If you have __init__.py files in all of your test subdirectories, you can have the same test filename show up in multiple directories. If you don’t, you can’t.

Here’s an example. Directory a and b both have the file, test_foo.py. It doesn’t matter what these files have in them, but for this example, they look like this:

``` py
#ch6/dups/a/test_foo.py

​ ​def​ ​test_a​():
​     ​pass​

# ch6/dups/b/test_foo.py

​ ​def​ ​test_b​():
​     ​pass​
```

With a directory structure like this:

``` md
​ dups
​ ├── a
​ │   └── test_foo.py
​ └── b
​     └── test_foo.py
```

These files don’t even have the same content, but it’s still mucked up. Running them individually will be fine, but running pytest from the dups directory won’t work:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch6/dups​
​ ​$ ​​pytest​​ ​​a​
​ =================== test session starts ===================
​ plugins: cov-2.5.1
​ collected 1 item
​
​ a/test_foo.py .                                     [100%]
​
​ ================ 1 passed in 0.01 seconds =================
​ ​$ ​​pytest​​ ​​b​
​ =================== test session starts ===================
​ plugins: cov-2.5.1
​ collected 1 item
​
​ b/test_foo.py .                                     [100%]
​
​ ================ 1 passed in 0.01 seconds =================
​ ​$ ​​pytest​
​ =================== test session starts ===================
​ plugins: cov-2.5.1
​ collected 1 item / 1 errors
​
​ ========================= ERRORS ==========================
​ _____________ ERROR collecting b/test_foo.py ______________
​ import file mismatch:
​ imported module 'test_foo' has this __file__ attribute:
​   /path/to/code/ch6/dups/a/test_foo.py
​ which is not the same as the test file we want to collect:
​   /path/to/code/ch6/dups/b/test_foo.py
​ HINT: remove __pycache__ / .pyc files
​       and/or use a unique basename for your test file modules
​ !!!!!!!!! Interrupted: 1 errors during collection !!!!!!!!!
​ ================= 1 error in 0.15 seconds =================
```

That error message highlights that we have two files named the same, but doesn’t tell us how to fix it.

To fix this test, just add empty __init__.py files in the subdirectories. Here, the example directory dups_fixed is the same as dups, but with __init__.py files added:

``` md
​ dups_fixed/
​ ├── a
​ │   ├── __init__.py
​ │   └── test_foo.py
​ └── b
​     ├── __init__.py
​     └── test_foo.py
```

Now, let’s try this again from the top level in dups_fixed:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch6/dups_fixed​
​ ​$ ​​pytest​
​ =================== test session starts ===================
​ plugins: cov-2.5.1
​ collected 2 items
​
​ a/test_foo.py .                                     [ 50%]
​ b/test_foo.py .                                     [100%]
​
​ ================ 2 passed in 0.03 seconds =================
```

There, all better. You might say to yourself that you’ll never have duplicate filenames, so it doesn’t matter. That’s fine. But projects grow and test directories grow, and do you really want to wait until it happens to you before you fix it? I say just put those files in there as a habit and don’t worry about it again.
