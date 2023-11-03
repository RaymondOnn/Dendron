---
id: aacnd6to67t17mnp35sgzix
title: run
desc: ''
updated: 1698524973468
created: 1698426929324
---

### Running tests

- To run tests, open a terminal and run the following command from the folder where the tests scripts are

  ``` sh
  pytest    # to run all test
  ```

- To run unit tests on a specific directory, file, or function
- Pytest will find all the test files in the current directory and subdirectories and then run the test functions.

  ```sh
   $ ​​pytest​​ ​​-v​​ ​​tasks/test_four.py::test_asdict​
  ​ ===================== test session starts ======================
  ​ collected 1 item
  ​ 
  ​ tasks/test_four.py::test_asdict PASSED                   [100%]
  ​ 
  ​ =================== 1 passed in 0.01 seconds ===================
  ​ ​$ ​​pytest​​ ​​tasks/test_three.py​​ ​​tasks/test_four.py​

  ​ ===================== test session starts ======================
  ​ collected 4 items
  ​
  ​ tasks/test_three.py ..                                   [ 50%]
  ​ tasks/test_four.py ..                                    [100%]
  ​
  ​ =================== 4 passed in 0.02 seconds ===================
  ​ ​$ ​​pytest​​ ​​tasks​
  ​ ===================== test session starts ======================
  ​ collected 4 items
  ​
  ​ tasks/test_four.py ..                                    [ 50%]
  ​ tasks/test_three.py ..                                   [100%]
  ​
  ​ =================== 4 passed in 0.02 seconds ===================
  ​ ​$ ​​cd​​ ​​tasks​
  ​ ​$ ​​pytest​
  ​ ===================== test session starts ======================
  ​ collected 4 items
  ​
  ​ test_four.py ..                                          [ 50%]
  ​ test_three.py ..                                         [100%]
  ​
  ​ =================== 4 passed in 0.02 seconds ===================

  ```
  

#### Outcomes

- The test output returns the total number of tests run, the number of tests passed, skipped, and failed, the total time taken to run the tests, and the failure stack trace if any.
- Here are the possible outcomes of a test function:
  - PASSED (.): The test ran successfully.
  - FAILED (F): The test did not run successfully (or XPASS + strict).
  - SKIPPED (s): The test was skipped. You can tell pytest to skip a test by using either the @pytest.mark.skip() or pytest.mark.skipif() decorators, discussed in ​Skipping Tests​.
  - xfail (x): The test was not supposed to pass, ran, and failed. You can tell pytest that a test is expected to fail by using the @pytest.mark.xfail() decorator, discussed in ​Marking Tests as Expecting to Fail​.
  - XPASS (X): The test was not supposed to pass, ran, and passed.
  - ERROR (E): An exception happened outside of the test function, in either a fixture, discussed in Chapter 3, ​pytest Fixtures​, or in a hook function, discussed in Chapter 5, ​Plugins​.

#### [Other Options](https://medium.com/@pragprog/using-options-aa2d3b6dd4a1)

- There are more customization that can be appended to the command, such as
  - `-x`: exit instantly or fail fast, to stop all unit tests upon encountering the first test failure
  - `-k` "keyword": specify the keyword(s) to selectively run tests, can match file name or function name, and can contain and and not statements
  - `--ff`: failed first, to run all tests starting from those that failed the last run (preferred)
  - `--lf`: last failed, to run tests that failed the last run (drawback: may not discover failures in tests that previously passed)
  - `--sw`: step-wise, stop at the first test failure and continue from there in the next run (drawback: may not discover failures in tests that previously passed)
- If the unit tests are taking too long to run, you can run them in parallel instead of sequentially! Install the pytest-xdist Python package and add this to the command when running unit tests,

  - `-n <number of workers>`: number of workers to run the tests in parallel
- Debugging error:
  - You might face the error `ModuleNotFoundError`` when your test scripts import from a folder from the base directory or whichever source directory.
  - For instance, your function resides in src/sample_file.py and your test scripts residing in tests/ directory perform an import from src.sample_file import sample_function.
  - To overcome this, create a configuration file pytest.ini in the base directory to indicate the directory to perform the import relative to the base directory. A sample of the content to add to the configuration file is as follows,

    ``` raw
    [pytest]
    pythonpath = .
    ```
--- 
### Running a Subset of Tests

#### A Single Directory

To run all the tests from one directory, use the directory as a parameter to pytest:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj​
​ ​$ ​​pytest​​ ​​tests/func​​ ​​--tb=no​
​ =================== test session starts ===================
​ collected 50 items
​
​ tests/func/test_add.py ..                           [  4%]
​ tests/func/test_add_variety.py .................... [ 44%]
​ ............                                        [ 68%]
​ tests/func/test_api_exceptions.py .......           [ 82%]
​ tests/func/test_unique_id_1.py F                    [ 84%]
​ tests/func/test_unique_id_2.py s.                   [ 88%]
​ tests/func/test_unique_id_3.py s.                   [ 92%]
​ tests/func/test_unique_id_4.py xxX.                 [100%]
​
​  1 failed, 44 passed, 2 skipped, 2 xfailed, 1 xpassed in 0.41 seconds
```

An important trick to learn is that using -v gives you the syntax for how to run a specific directory, class, and test.

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​tests/func​​ ​​--tb=no​
​ =================== test session starts ===================
​ collected 50 items
​
​ tests/func/test_add.py::test_add_returns_valid_id PASSED [  2%]
​ tests/func/test_add.py::test_added_task_has_id_set PASSED [  4%]
​ ​...​
​ tests/func/test_api_exceptions.py::test_add_raises PASSED [ 70%]
​ tests/func/test_api_exceptions.py::test_list_raises PASSED [ 72%]
​ tests/func/test_api_exceptions.py::test_get_raises PASSED [ 74%]
​ ​...​
​ tests/func/test_unique_id_1.py::test_unique_id FAILED [ 84%]
​ tests/func/test_unique_id_2.py::test_unique_id_1 SKIPPED [ 86%]
​ tests/func/test_unique_id_2.py::test_unique_id_2 PASSED [ 88%]
​ ​...​
​ tests/func/test_unique_id_4.py::test_unique_id_1 xfail [ 94%]
​ tests/func/test_unique_id_4.py::test_unique_id_is_a_duck xfail [ 96%]
​ tests/func/test_unique_id_4.py::test_unique_id_not_a_duck XPASS [ 98%]
​ tests/func/test_unique_id_4.py::test_unique_id_2 PASSED [100%]
​
​  1 failed, 44 passed, 2 skipped, 2 xfailed, 1 xpassed in 0.48 seconds
```

You’ll see the syntax listed here in the next few examples.

#### A Single Test File/Module

To run a file full of tests, list the file with the relative path as a parameter to pytest:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj​
​ ​$ ​​pytest​​ ​​tests/func/test_add.py​
​ =================== test session starts ===================
​ collected 2 items
​
​ tests/func/test_add.py ..                           [100%]
​
​ ================ 2 passed in 0.10 seconds =================
```

We’ve been doing this for a while.

#### A Single Test Function

To run a single test function, add :: and the test function name:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj​
​ ​$ ​​pytest​​ ​​-v​​ ​​tests/func/test_add.py::test_add_returns_valid_id​
​ =================== test session starts ===================
​ collected 1 item
​
​ tests/func/test_add.py::test_add_returns_valid_id PASSED [100%]
​
​ ================ 1 passed in 0.04 seconds =================
```

Use -v so you can see which function was run.

#### A Single Test Class

Test classes are a way to group tests that make sense to be grouped together. Here’s an example:

``` py
ch2/tasks_proj/tests/func/test_api_exceptions.py

​ ​class​ TestUpdate():
​     ​"""Test expected exceptions with tasks.update()."""​
​
​     ​def​ ​test_bad_id​(self):
​         ​"""A non-int id should raise an excption."""​
​         ​with​ pytest.raises(TypeError):
​             tasks.update(task_id={​'dict instead'​: 1},
​                          task=tasks.Task())
​
​     ​def​ ​test_bad_task​(self):
​         ​"""A non-Task task should raise an excption."""​
​         ​with​ pytest.raises(TypeError):
​             tasks.update(task_id=1, task=​'not a task'​)
```

Since these are two related tests that both test the update() function, it’s reasonable to group them in a class. To run just this class, do like we did with functions and add ::, then the class name to the file parameter:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj​
​ ​$ ​​pytest​​ ​​-v​​ ​​tests/func/test_api_exceptions.py::TestUpdate​
​ =================== test session starts ===================
​ collected 2 items
​
​ tests/func/test_api_exceptions.py::TestUpdate::test_bad_id PASSED [ 50%]
​ tests/func/test_api_exceptions.py::TestUpdate::test_bad_task PASSED [100%]
​
​ ================ 2 passed in 0.02 seconds =================
```

#### A Single Test Method of a Test Class

If you don’t want to run all of a test class — just one method — just add another :: and the method name:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj​
​ ​$ ​​pytest​​ ​​-v​​ ​​tests/func/test_api_exceptions.py::TestUpdate::test_bad_id​
​ =================== test session starts ===================
​ collected 1 item
​
​ tests/func/test_api_exceptions.py::TestUpdate::test_bad_id PASSED [100%]
​
​ ================ 1 passed in 0.02 seconds =================
```

#### A Set of Tests Based on Test Name

- The -k option enables you to pass in an expression to run tests that have certain names specified by the expression as a substring of the test name.
- You can use **and**, **or**, and **not** in your expression to create complex expressions.

For example, we can run all of the functions that have _raises in their name:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch2/tasks_proj​
​ ​$ ​​pytest​​ ​​-v​​ ​​-k​​ ​​_raises​
​ =================== test session starts ===================
​ collected 56 items / 51 deselected
​
​ tests/func/test_api_exceptions.py::test_add_raises PASSED [ 20%]
​ tests/func/test_api_exceptions.py::test_list_raises PASSED [ 40%]
​ tests/func/test_api_exceptions.py::test_get_raises PASSED [ 60%]
​ tests/func/test_api_exceptions.py::test_delete_raises PASSED [ 80%]
​ tests/func/test_api_exceptions.py::test_start_tasks_db_raises PASSED [100%]
​
​ ========= 5 passed, 51 deselected in 0.13 seconds =========
```

We can use and and not to get rid of the test_delete_raises() from the session:

``` sh
​ ​$ ​​pytest​​ ​​-v​​ ​​-k​​ ​​"_raises and not delete"​
​ =================== test session starts ===================
​ collected 56 items / 52 deselected
​
​ tests/func/test_api_exceptions.py::test_add_raises PASSED [ 25%]
​ tests/func/test_api_exceptions.py::test_list_raises PASSED [ 50%]
​ tests/func/test_api_exceptions.py::test_get_raises PASSED [ 75%]
​ tests/func/test_api_exceptions.py::test_start_tasks_db_raises PASSED [100%]
​
​ ========= 4 passed, 52 deselected in 0.12 seconds =========
```
