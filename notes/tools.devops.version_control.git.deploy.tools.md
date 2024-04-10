---
id: h910zxm4alebyzl0xx6mgaa
title: tools
desc: ''
updated: 1710965169389
created: 1710961711702
---
https://www.youtube.com/watch?v=ObksvAZyWdo
### Git hooks
- Scripts that execute based on certain events occur in the `git` lifecycle 
- Allows you trigger custom actions at key points in your workflow. 
- Two main groups of these hooks: client-side hooks and server-side hooks. 
- Can be triggered by 
    - client-side hooks: operations on the local computer such as committing and merging: 
    - server-side hooks: network operations on the Git server such as receiving pushed commits. 
- Used for all kinds of use cases, i.e enforcing code style, enforcing a commit message convention and even checking for typos in commit messages.
- Can be written in various programming languages, Just remember to change the shebang on the first line.

#### Local `git` hooks 
- Stored in ` .git/hooks`. 
    - Usually contain sample scripts created by `git init`
    - To enable a hook script, remove the `.sample` extension and `chmod u+x` it 

##### Sharing git hooks
- Being local to the Git repository means they are not cloned with the project and are not version-controlled.
- Hence, a workaround is to create a `hooks` folder in the project root, outside `.git` folder
    -  This ensure that hooks can be versioned with the rest of the code
    -  Each developer would just copy and paste the files into their local `.git/hooks` directory.


> NOTE: Hooks need to be executable for them to work. 
> To change your file permissions:
>    ``` bash
>    chmod +x <HOOK_SCRIPT>
>    ```
>Your hook should now be run the next time you perform a commit.

#### The Pre-commit project
- link: https://pre-commit.com/
- Pre-commit supports an impressive number of hooks, everything from blackening code blocks in Python documentation to linting commit messages
    1. Install the pre-commit pac-man that will take care of installing requirements:
        ``` sh
        $ pip install pre-commit
        ```
    2. To setup pre-commit in your git repository, it needs a `.pre-commit-config.yaml` file, which specifies top-level options, repos and hooks from each repo. 
        ``` yaml
        # Example: Here we add some checks for Python, JSON and JS
        repos:
        -   repo: https://github.com/pre-commit/pre-commit-hooks
            rev: v1.2.3
            hooks:
            -   id: autopep8-wrapper
            -   id: pretty-format-json
        -   repo: https://github.com/pre-commit/mirrors-eslint
            rev: v5.0.1
            hooks:
            -   id: eslint
        ```    

    3. Install pre-commit to `.git/hooks` directory by running pre-commit’s `install` command inside the root directory of your repository:
        ``` sh
        $ pre-commit install
        >>> pre-commit installed at /Users/prateekshasingh/Frappe/frappe-bench/apps/hub/.git/hooks/pre-commit
        ```

    4. Go ahead and commit.
<br>
- Recommended hooks:
    ``` yaml
    repos:
    -   repo: https://github.com/psf/black
        rev: 21.9b0
        hooks:
        -   id: black
            args: [--safe]
    -   repo: https://github.com/pre-commit/pre-commit-hooks
        rev: v3.2.0
        hooks:
        - id: trailing-whitespace
        - id: end-of-file-fixer
        - id: check-yaml
        - id: check-added-large-files
        -   id: debug-statements
            language_version: python3
    -   repo: https://github.com/PyCQA/flake8
        rev: 3.9.2
        hooks:
        -   id: flake8
            language_version: python3
    -   repo: https://github.com/asottile/reorder_python_imports
        rev: v2.6.0
        hooks:
        -   id: reorder-python-imports
            args: [--application-directories=.:src, --py36-plus]      
    -   repo: https://github.com/asottile/pyupgrade
        rev: v2.20.0
        hooks:
        -   id: pyupgrade
            args: [--py36-plus]
    -   repo: https://github.com/pre-commit/mirrors-mypy
        rev: v0.910
        hooks:
        -   id: mypy
            files: ^src/
            args: []                
    ```    