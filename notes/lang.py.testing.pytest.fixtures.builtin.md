---
id: belarnrfp1os5hw23whqr1y
title: builtin
desc: ''
updated: 1698794963579
created: 1698794250007
---

### tmpdir: temporary file system directory

- The tmpdir and tmpdir_factory builtin fixtures are used to create a temporary file system directory before your test runs, and remove the directory when your test is finished.
- If you’re testing something that reads, writes, or modifies files, you can use tmpdir to create files or directories used by a single test, and you can use tmpdir_factory when you want to set up a directory for many tests.
- The tmpdir fixture has function scope, and the tmpdir_factory fixture has session scope.
- Any individual test that needs a temporary directory or file just for the single test can use tmpdir.
- This is also true for a fixture that is setting up a directory or file that should be recreated for each test function.

Here’s a simple example using tmpdir:

``` py
ch4/test_tmpdir.py

​ ​def​ ​test_tmpdir​(tmpdir):
​     ​# tmpdir already has a path name associated with it​
​     ​# join() extends the path to include a filename​
​     ​# the file is created when it's written to​
​     a_file = tmpdir.join(​'something.txt'​)
​ 
​     ​# you can create directories​
​     a_sub_dir = tmpdir.mkdir(​'anything'​)
​ 
​     ​# you can create files in directories (created when written)​
​     another_file = a_sub_dir.join(​'something_else.txt'​)
​ 
​     ​# this write creates 'something.txt'​
​     a_file.write(​'contents may settle during shipping'​)
​ 
​     ​# this write creates 'anything/something_else.txt'​
​     another_file.write(​'something different'​)
​ 
​     ​# you can read the files as well​
​     ​assert​ a_file.read() == ​'contents may settle during shipping'​
​     ​assert​ another_file.read() == ​'something different'​
```

- The value returned from tmpdir is an object of type py.path.local.[9]
- Because the tmpdir fixture is defined as function scope, **you can’t use tmpdir to create folders or files that should stay in place longer than one test function.**
- For fixtures with scope other than function (class, module, session), tmpdir_factory is available.

- The tmpdir_factory fixture is a lot like tmpdir, but it has a different interface.

To see how similar tmpdir and tmpdir_factory are, I’ll modify the tmpdir example just enough to use tmpdir_factory instead:

``` py
ch4/test_tmpdir.py

​ ​def​ ​test_tmpdir_factory​(tmpdir_factory):
​     ​# you should start with making a directory​
​     ​# a_dir acts like the object returned from the tmpdir fixture​
​     a_dir = tmpdir_factory.mktemp(​'mydir'​)
​
​     ​# base_temp will be the parent dir of 'mydir'​
​     ​# you don't have to use getbasetemp()​
​     ​# using it here just to show that it's available​
​     base_temp = tmpdir_factory.getbasetemp()
​     ​print​(​'base:'​, base_temp)
​
​     ​# the rest of this test looks the same as the 'test_tmpdir()'​
​     ​# example except I'm using a_dir instead of tmpdir​
​
​     a_file = a_dir.join(​'something.txt'​)
​     a_sub_dir = a_dir.mkdir(​'anything'​)
​     another_file = a_sub_dir.join(​'something_else.txt'​)
​
​     a_file.write(​'contents may settle during shipping'​)
​     another_file.write(​'something different'​)
​
​     ​assert​ a_file.read() == ​'contents may settle during shipping'​
​     ​assert​ another_file.read() == ​'something different'​
```

The first line uses mktemp(’mydir’) to create a directory and saves it in a_dir. For the rest of the function, you can use a_dir just like the tmpdir returned from the tmpdir fixture.

In the second line of the tmpdir_factory example, the getbasetemp() function returns the base directory used for this session. The print statement is in the example so you can see where the directory is on your system. Let’s see where it is:

``` sh
​ ​$ ​​cd​​ ​​/path/to/code/ch4​
​ ​$ ​​pytest​​ ​​-q​​ ​​-s​​ ​​test_tmpdir.py::test_tmpdir_factory​
​ base: /private/var/folders/53/zv4j_zc506x2xq25l31qxvxm0000gn\
​       /T/pytest-of-okken/pytest-732
​ .
​ 1 passed in 0.04 seconds
```

This base directory is system- and user-dependent, and pytest-NUM changes with an incremented NUM for every session. The base directory is left alone after a session, but pytest cleans them up and only the most recent few temporary base directories are left on the system, which is great if you need to inspect the files after a test run.

You can also specify your own base directory if you need to with pytest — basetemp=mydir.

#### Using Temporary Directories for Other Scopes
We get session scope temporary directories and files from the tmpdir_factory fixture, and function scope directories and files from the tmpdir fixture. But what about other scopes? What if we need a module or a class scope temporary directory? To do this, we create another fixture of the scope we want and have it use tmpdir_factory.

For example, suppose we have a module full of tests, and many of them need to be able to read some data from a json file. We could put a module scope fixture in either the module itself, or in a conftest.py file that sets up the data file like this:

``` py
ch4/authors/conftest.py

​ ​"""Demonstrate tmpdir_factory."""​
​ 
​ ​import​ ​json​
​ ​import​ ​pytest​
​ 
​ 
​ @pytest.fixture(scope=​'module'​)
​ ​def​ ​author_file_json​(tmpdir_factory):
​     ​"""Write some authors to a data file."""​
​     python_author_data = {
​         ​'Ned'​: {​'City'​: ​'Boston'​},
​         ​'Brian'​: {​'City'​: ​'Portland'​},
​         ​'Luciano'​: {​'City'​: ​'Sau Paulo'​}
​     }
​ 
​     file_ = tmpdir_factory.mktemp(​'data'​).join(​'author_file.json'​)
​     ​print​(​'file:{}'​.format(str(file_)))
​ 
​     ​with​ file.open(​'w'​) ​as​ f:
​         json.dump(python_author_data, f)
​     ​return​ file
```

The author_file_json() fixture creates a temporary directory called data and creates a file called author_file.json within the data directory. It then writes the python_author_data dictionary as json. Because this is a module scope fixture, the json file will only be created once per module that has a test using it:

``` py
ch4/authors/test_authors.py

​ ​"""Some tests that use temp data files."""​
​ ​import​ ​json​
​ 
​ 
​ ​def​ ​test_brian_in_portland​(author_file_json):
​     ​"""A test that uses a data file."""​
​     ​with​ author_file_json.open() ​as​ f:
​         authors = json.load(f)
​     ​assert​ authors[​'Brian'​][​'City'​] == ​'Portland'​
​ 
​ 
​ ​def​ ​test_all_have_cities​(author_file_json):
​     ​"""Same file is used for both tests."""​
​     ​with​ author_file_json.open() ​as​ f:
​         authors = json.load(f)
​     ​for​ a ​in​ authors:
​         ​assert​ len(authors[a][​'City'​]) > 0
```

Both tests will use the same json file. If one test data file works for multiple tests, there’s no use recreating it for both.
