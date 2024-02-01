---
id: 7dk0lz5kd03vlhzpbcdpwjw
title: pex
desc: ''
updated: 1702602185177
created: 1699523935084
---
https://www.pither.com/simon/blog/2018/09/18/how-build-portable-executable-single-python-script
https://docs.gruntwork.io/reference/modules/terraform-aws-utilities/prepare-pex-environment/

-   Medium article: [pex — python executables](https://medium.com/ovni/pex-python-executables-c0ea39cee7f1)
-   [Criteo;s Use with Pyspark](https://medium.com/criteo-engineering/packaging-code-with-pex-a-pyspark-example-9057f9f144f3)
-   [Man Page](https://manpages.debian.org/testing/pex/pex.1.en.html)

## PEX

### What is PEX

-   Python Executables
-   Basically a zip file containing the whole virtual environment

```bash
cat <<EOF > abc.py
if __name__ == '__main__':
    print('hello')
EOF
python abc.py
>>> hello
python .
>>> Error: cannot find "__main__" module in "."
mv abc.py __main__.py
python .
>>> hello

# python can execute directly in zip file
zip abc.zip __main__.py
python abc.zip
>>> hello

# the extension actually doesn't matter
mv abc.zip abc.pex
python abc.pex
>>> hello

# make it a bash executable.
cat <(echo '#!/usr/bin/env python') __main__.py
./__main__.py
>>> hello

mv __main__.py xyz
python ./xyz
>>> hello
```

### Why PEX

-   Isolated
    ```bash
    pip install pandas
    pex dagster -o dagster.pex
    ./dagster.pex
    >>> import dagster # OK
    >>> import pandas  # ImportError
    ```
-   Composable
    ```bash
    pex dagster -o dagster.pex
    pex pandas -o pandas.pex
    export PEX_PATH=pandas.pex
    ./dagster.pex
    >>> import dagster # OK
    >>> import pandas  # OK
    ```
-   Deterministic
    ```bash
    pex dagster -o dagster.pex | sha256sum
    >>> e3b0c4429...
    pex pandas -o pandas.pex | sha256sum
    >>> e3b0c4429...
    ```

### Packaging Modules

-   General structure:
    ```bash
    # packaging modules into a pex file
    pex <MODULE_1> <MODULE_2> <MODULE_3> -o myenv.pex
    ```
-   To use this myenv.pex (which now has all your required modules available), simply run:
    ```bash
    ./myenv.pex
    ```
-   By default, this will just spin up python itself, then you can import the modules you’ve added to the .pex file or, to run a script, do a simple
    ```bash
    ./myenv.pex script.py
    ```
-   Using a requirements.txt file may be easier to create your environment though
    ```bash
    pex -r requirements.txt -o myenv.pex
    ```

### [Packaging Scripts](https://www.shearn89.com/2021/04/15/pex-file-creation#packages-with-multiple-modules)

#### Initial setup

1. Create Virtual Environment. Ensure to install `pex`

    ```bash
    mkdir pex-test
    cd pex-test
    pip install --user virtualenv
    virtualenv .venv
    source .venv/bin/activate
    pip install pex
    ```

2. Suppose this is the script, `main.py`

    ```py
     #!/usr/bin/env python3 <- this is required
     import requests

     def hello():
     print('hello world')

     if __name__=="__main__":
     hello()
    ```

3. Installing dependencies and running script

    ```bash
     echo 'requests' > requirements.txt
     pip install -r requirements.txt

     python main.py
     >>> hello world
    ```

#### Building PEX file

-   PEX requires things to be modules for them to get bundled in, so we need to create a setup.py file:

    ```py
    # setup.py
    from distutils.core import setup
    setup(
        name='pexTest',
        version='1.0',
        scripts=['main.py'],
    )
    ```

-   Attempting to create the binary file
    ```bash
    pex . -r requirements.txt -c main.py -o test.pex
    ./test.pex
    >>> hello world
    ```

#### Adding Modules

-   Adding a custom module
    ```bash
      mkdir importTest
      touch importTest/__init__.py
      cat <EOF>>importTest/hello.py
      def helloFoo():
          print("hello foo")
      EOF
    ```
-   Updating `main.py`

    ```py
      import requests
      from importTest import hello

      def helloWorld():
          print('hello world')

      if __name__=="__main__":
          helloWorld()
          hello.helloFoo() # <-- NEW UPDATE
    ```

    -   Running the script, we should see the extra call

    ```bash
      python main.py
      >>> hello world
      >>> hello foo
    ```

-   With a new dependencies, we update `setup.py`
    ```py
      from distutils.core import setup
      setup(
          name='pexTest',
          version='1.0',
          packages=['importTest'],  # <-- NEW UPDATE
          scripts=['main.py'],
      )
    ```
-   Now we build and test the `pex` file again

    ```bash
      pex . -r requirements.txt -c main.py -o test.pex && ./test.pex
      >>> hello world
      >>> hello foo
    ```

    -   FYI, the file structure

    ```md
    pex-test
    │ ├── venv
    │ └── importTest
    │      ├── __init__.py
    │      └── hello.py
    ├── main.py
    ├── requirements.txt
    ├── setup.py
    └── test.pex
    ```

#### Adding in non-packaged scripts

-   Lastly, if you do want to have top-level scripts, you just need to add them as modules in setup.py:

    ```bash
    cat <EOF>>extras.py
    def extraFunction():
    print("hello from the extra function")
    EOF
    ```

-   Update `main.py` & `setup.py`

    ```py
    # main.py
      import requests
      import importTest
      import extras

      def helloWorld():
          print('hello world')

      if __name__=="__main__":
          helloWorld()
          importTest.hello.helloFoo()
          extras.extraFunction()

      # setup.py
      from distutils.core import setup
      setup(
          name='pexTest',
          version='1.0',
          py_modules=['extras'],   # <-- NEW UPDATE
          packages=['importTest'],
          scripts=['main.py'],
      )
    ```

-   Rebuild and test pex file
    ```bash
      pex . -r requirements.txt -c main.py -o test.pex && ./test.pex
      >>> hello world
      >>> hello foo
      >>> goodbye
      >>> hello from the extra function
    ```

### Pex and Docker
https://dagster.io/blog/fast-deploys-with-pex-and-docker

Docker can build Python packages for Linux on any OS because the target Linux OS and Python interpreter are available during the build process. pex can only build pex files for Linux for packages that provides wheels. As as fallback we use a Docker container during the build to handle source distributions. This step could be moved into a separate shared service in the future.
https://medium.com/@kosala.atapattu/docker-build-for-python-abd7f91d4896