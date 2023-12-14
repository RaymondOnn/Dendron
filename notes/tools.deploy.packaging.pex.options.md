---
id: 8iykk1y5wsln0h7v1u7usmh
title: options
desc: ''
updated: 1701855241424
created: 1700745674118
---

``` bash
root@6ffe97ddd95a:/opt/spark# pex --help
usage: pex [-o OUTPUT.PEX] [options] [-- arg1 arg2 ...]

pex builds a PEX (Python Executable) file based on the given specifications: sources, requirements, their dependencies and other options.
Command-line options can be provided in one or more files by prefixing the filenames with an @ symbol. These files must contain one argument per line.

positional arguments:
  requirements          Requirements to add to the pex (default: None)

options:
  -h, --help            show this help message and exit
  -V, --version         show program's version number and exit
  -o PEX_NAME, --output-file PEX_NAME
                        The name of the generated .pex file: Omitting this will run PEX immediately and not save it to a file. (default: None)
  -p FILE, --preamble-file FILE
                        The name of a file to be included as the preamble for the generated .pex file (default: None)
  -D DIR, --sources-directory DIR
                        Add a directory containing sources and/or resources to be packaged into the generated .pex file. This option can be used multiple times. (default: [])
  -R DIR, --resources-directory DIR
                        Add resources directory to be packaged into the generated .pex file. This option can be used multiple times. DEPRECATED: Use -D/--sources-directory instead. (default: [])
  -r FILE or URL, --requirement FILE or URL
                        Add requirements from the given requirements file. This option can be used multiple times. (default: [])
  --constraints FILE or URL
                        Add constraints from the given constraints file. This option can be used multiple times. (default: [])
  --requirements-pex FILE
                        Add requirements from the given .pex file. This option can be used multiple times. (default: [])
  --seed [{none,args,verbose}]
                        Seed local Pex caches for the generated PEX and print out the command line to run directly from the seed with (args) or else a json object including the 'pex_root' path, the 'python' binary path and the
                        seeded 'pex' path (verbose). (default: none)
  --no-seed             Deprecated: Use --seed=none instead. (default: None)
  --help-variables      Print out help about the various environment variables used to change the behavior of a running PEX file. (default: None)

Resolver options:
  Tailor how to find, resolve and translate the packages that get put into the PEX environment.

  --resolver-version {pip-legacy-resolver,pip-2020-resolver}
                        The dependency resolver version to use. Read more at https://pip.pypa.io/en/stable/user_guide/#resolver-changes-2020 (default: pip-legacy-resolver)
  --pypi, --no-pypi, --no-index
                        Whether to use PyPI to resolve dependencies. (default: True)
  -f PATH/URL, --find-links PATH/URL, --repo PATH/URL
                        Additional repository path (directory or URL) to look for requirements. (default: None)
  -i URL, --index URL, --index-url URL
                        Additional cheeseshop indices to use to satisfy requirements. (default: None)
  --retries RETRIES     Maximum number of retries each connection should attempt. (default: 5)
  --timeout SECS        Set the socket timeout in seconds. (default: 15)
  --proxy PROXY         Specify a proxy in the form http(s)://[user:passwd@]proxy.server:port. (default: None)
  --cert PATH           Path to alternate CA bundle. (default: None)
  --client-cert PATH    Path to an SSL client certificate which should be a single file containing the private key and the certificate in PEM format. (default: None)
  --cache-ttl DEPRECATED
                        Deprecated: No longer used. (default: None)
  -H DEPRECATED, --header DEPRECATED
                        Deprecated: No longer used. (default: None)
  --pex-repository FILE
                        Resolve requirements from the given PEX file instead of from --index servers or --find-links repos. (default: None)
  --pre, --no-pre       Whether to include pre-release and development versions of requirements. (default: False)
  --wheel, --no-wheel, --no-use-wheel
                        Whether to allow wheel distributions. (default: True)
  --build, --no-build   Whether to allow building of distributions from source. (default: True)
  --transitive, --no-transitive, --intransitive
                        Whether to transitively resolve requirements. (default: True)
  -j JOBS, --jobs JOBS  The maximum number of parallel jobs to use when resolving, building and installing distributions. You might want to increase the maximum number of parallel jobs to potentially improve the latency of the
                        pex creation process at the expense of other processes on your system. (default: 12)
  --pex-path PEX_PATH   A colon separated list of other pex files to merge into the runtime environment. (default: None)

PEX output options:
  Tailor the behavior of the emitted .pex file if -o is specified.

  --include-tools       Whether to include runtime tools in the pex file. If included, these can be run by exporting PEX_TOOLS=1 and following the usage and --help information. (default: False)
  --zip-safe, --not-zip-safe
                        Deprecated: This option is no longer used since user code is now always unzipped before execution. (default: None)
  --layout {zipapp,packed,loose}
                        By default, a PEX is created as a single file zipapp when `-o` is specified, but either a packed or loose directory tree based layout can be chosen instead. A packed layout PEX is an executable directory
                        structure designed to have cache-friendly characteristics for syncing incremental updates to PEXed applications over a network. At the top level of the packed directory tree there is an executable
                        `__main__.py`script. The directory can also be executed by passing its path to a Python executable; e.g: `python packed-pex-dir/`. The Pex bootstrap code and all dependency code are packed into
                        individual zip files for efficient caching and syncing. A loose layout PEX is similar to a packed PEX, except that neither the Pex bootstrap code nor the dependency code are packed into zip files, but
                        are instead present as collections of loose files in the directory tree providing different caching and syncing tradeoffs. Both zipapp and packed layouts install themselves in the PEX_ROOT as loose apps
                        by default before executing, but these layouts compose with `--venv` execution mode as well and support `--seed`ing. (default: zipapp)
  --unzip, --no-unzip   Deprecated: This option is no longer used since unzipping PEX zip files before execution is now the default. (default: None)
  --venv [{prepend,append}]
                        Convert the pex file to a venv before executing it. If 'prepend' or 'append' is specified, then all scripts and console scripts provided by distributions in the pex file will be added to the PATH in the
                        corresponding position. If the the pex file will be run multiple times under a stable runtime PEX_ROOT, the venv creation will only be done once and subsequent runs will enjoy lower startup latency.
                        (default: False)
  --venv-copies, --no-venv-copies
                        If --venv is specified, create the venv using copies of base interpreter files instead of symlinks. This allows --venv mode PEXes to work across interpreter upgrades without being forced to remove the
                        PEX_ROOT to allow the venv to re-build using the upgraded interpreter. (default: False)
  --venv-site-packages-copies, --no-venv-site-packages-copies
                        If --venv is specified, populate the venv site packages using hard links or copies of resolved PEX dependencies instead of symlinks. This can be used to work around problems with tools or libraries that
                        are confused by symlinked source files. (default: False)
  --always-write-cache  Deprecated: This option is no longer used; all internally cached distributions in a PEX are always installed into the local Pex dependency cache. (default: None)
  --ignore-errors       Ignore requirement resolution solver errors when building pexes and later invoking them. (default: False)
  --inherit-path {false,prefer,fallback}
                        Inherit the contents of sys.path (including site-packages, user site-packages and PYTHONPATH) running the pex. Possible values: false (does not inherit sys.path), fallback (inherits sys.path after
                        packaged dependencies), prefer (inherits sys.path before packaged dependencies), No value (alias for prefer, for backwards compatibility). (default: false)
  --compile, --no-compile
                        Compiling means that the built pex will include .pyc files, which will result in slightly faster startup performance. However, compiling means that the generated pex likely will not be reproducible,
                        meaning that if you were to run `./pex -o` with the same inputs then the new pex would not be byte-for-byte identical to the original. (default: False)
  --use-system-time, --no-use-system-time
                        Use the current system time to generate timestamps for the new pex. Otherwise, Pex will use midnight on January 1, 1980. By using system time, the generated pex will not be reproducible, meaning that if
                        you were to run `./pex -o` with the same inputs then the new pex would not be byte-for-byte identical to the original. (default: False)
  --runtime-pex-root RUNTIME_PEX_ROOT
                        Specify the pex root to be used in the generated .pex file (if unspecified, uses ~/.pex). (default: None)
  --strip-pex-env, --no-strip-pex-env
                        Strip all `PEX_*` environment variables used to control the pex runtime before handing off control to the pex entrypoint. You might want to set this to `False` if the new pex executes other pexes (or the
                        Pex CLI itself) and you want the executed pex to be controllable via `PEX_*` environment variables. (default: True)

PEX environment options:
  Tailor the interpreter and platform targets for the PEX environment.

  --python PYTHON       The Python interpreter to use to build the PEX (default: current interpreter). This cannot be used with `--interpreter-constraint`, which will instead cause PEX to search for valid interpreters. Either
                        specify an absolute path to an interpreter, or specify a binary accessible on $PATH like `python3.7`. This option can be passed multiple times to create a multi-interpreter compatible PEX. (default: [])
  --python-path PYTHON_PATH
                        Colon-separated paths to search for interpreters when `--interpreter-constraint` and/or `--resolve-local-platforms` are specified (default: $PATH). Each element can be the absolute path of an interpreter
                        binary or a directory containing interpreter binaries. (default: None)
  --interpreter-constraint INTERPRETER_CONSTRAINT
                        Constrain the selected Python interpreter. Specify with Requirement-style syntax, e.g. "CPython>=2.7,<3" (A CPython interpreter with version >=2.7 AND version <3), ">=2.7,<3" (Any Python interpreter with
                        version >=2.7 AND version <3) or "PyPy" (A PyPy interpreter of any version). This argument may be repeated multiple times to OR the constraints. Try `PEX_TOOLS=1 /usr/local/bin/python3.10
                        /usr/local/bin/pex interpreter --verbose --indent 4` to find the exact interpreter constraints of /usr/local/bin/python3.10 and `PEX_TOOLS=1 /usr/local/bin/pex interpreter --all --verbose --indent 4` to
                        find out the interpreter constraints of all Python interpreters on the $PATH. (default: [])
  --platform PLATFORMS  The platform for which to build the PEX. This option can be passed multiple times to create a multi-platform pex. To use the platform corresponding to the current interpreter you can pass `current`. To
                        target any other platform you pass a string composed of fields: <platform>-<python impl abbr>-<python version>-<abi>. These fields stem from wheel name conventions as outlined in
                        https://www.python.org/dev/peps/pep-0427#file-name-convention and influenced by https://www.python.org/dev/peps/pep-0425. For the current interpreter at /usr/local/bin/python3.10 the full platform string
                        is manylinux_2_31_x86_64-cp-310-cp310. To find out more, try `PEX_TOOLS=1 /usr/local/bin/pex interpreter --all --verbose --indent 4` to print out the platform for all interpreters on the $PATH or
                        `PEX_TOOLS=1 /usr/local/bin/python3.10 /usr/local/bin/pex interpreter --verbose --indent 4` to inspect the single interpreter /usr/local/bin/python3.10. (default: [])
  --manylinux [ASSUME_MANYLINUX], --no-manylinux [ASSUME_MANYLINUX], --no-use-manylinux [ASSUME_MANYLINUX]
                        Whether to allow resolution of manylinux wheels for linux target platforms. (default: manylinux2014)
  --resolve-local-platforms
                        When --platforms are specified, attempt to resolve a local interpreter that matches each platform specified. If found, use the interpreter to resolve distributions; if not (or if this option is not
                        specified), resolve for each platform only allowing matching binary distributions and failing if only sdists or non-matching binary distributions can be found. (default: False)
  --python-shebang PYTHON_SHEBANG
                        The exact shebang (#!...) line to add at the top of the PEX file minus the #!. This overrides the default behavior, which picks an environment Python interpreter compatible with the one used to build the
                        PEX file. (default: None)

PEX entry point options:
  Specify what target/module the PEX should invoke if any.

  -m MODULE[:SYMBOL], -e MODULE[:SYMBOL], --entry-point MODULE[:SYMBOL]
                        Set the entry point to module or module:symbol. If just specifying module, pex behaves like python -m, e.g. python -m SimpleHTTPServer. If specifying module:symbol, pex assume symbol is a n0-arg callable
                        and imports that symbol and invokes it as if via `sys.exit(symbol())`. (default: None)
  -c SCRIPT_NAME, --script SCRIPT_NAME, --console-script SCRIPT_NAME
                        Set the entry point as to the script or console_script as defined by a any of the distributions in the pex. For example: "pex -c fab fabric" or "pex -c mturk boto". (default: None)
  --validate-entry-point
                        Validate the entry point by importing it in separate process. Warning: this could have side effects. For example, entry point `a.b.c:m` will translate to `from a.b.c import m` during validation.
                        (default: False)

Global options:
  -v                    Turn on logging verbosity, may be specified multiple times. (default: 0)
  --emit-warnings, --no-emit-warnings
                        Emit runtime UserWarnings on stderr. If false, only emit them when PEX_VERBOSE is set. (default: True)
  --pex-root PEX_ROOT   Specify the pex root used in this invocation of pex (if unspecified, uses /root/.pex). (default: None)
  --disable-cache       Disable caching in the pex tool entirely. (default: False)
  --cache-dir CACHE_DIR
                        DEPRECATED: Use --pex-root instead. The local cache directory to use for speeding up requirement lookups. (default: None)
  --tmpdir TMPDIR       Specify the temporary directory Pex and its subprocesses should use. (default: /tmp)
  --rcfile RC_FILE      An additional path to a pexrc file to read during configuration parsing, in addition to reading `/etc/pexrc` and `~/.pexrc`. If `PEX_IGNORE_RCFILES=true`, then all rc files will be ignored. (default:
                        None)
```