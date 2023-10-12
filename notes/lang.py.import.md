---
id: 82b9k345pkczm5ba4xlurcg
title: import
desc: ''
updated: 1696940773863
created: 1694226568563
---

https://www.youtube.com/watch?v=QCSz0j8tGmI
https://www.geeksforgeeks.org/how-to-dynamically-load-modules-or-classes-in-python/




## Import via python script

``` py
import sys
import subprocess
import pkg_resources
from pkg_resources import DistributionNotFound, VersionConflict

def should_install_requirement(requirement):
    should_install = False
    try:
        pkg_resources.require(requirement)
    except (DistributionNotFound, VersionConflict):
        should_install = True
    return should_install


def install_packages(requirement_list):
    try:
        requirements = [
            requirement
            for requirement in requirement_list
            if should_install_requirement(requirement)
        ]
        if len(requirements) > 0:
            subprocess.check_call([sys.executable, "-m", "pip", "install", *requirements])
        else:
            print("Requirements already satisfied.")

    except Exception as e:
        print(e)

if __name__ == "__main__":
    requirement_list = ['requests', 'httpx==0.18.2']
    install_packages(requirement_list)        
```

#### Alternatives

```py
# To conditionally install multiple packages with exact version

import sys
from subprocess import run, PIPE, STDOUT
import pkg_resources

def run_cmd(cmd):
    ps = run(cmd, stdout=PIPE, stderr=STDOUT, shell=True, text=True)
    print(ps.stdout)


# packages to be conditionally installed with exact version
required = {"click==8.0.1", "semver==3.0.0.dev2"}
installed = {f"{pkg.key}=={pkg.version}" for pkg in pkg_resources.working_set}
missing = required - installed

if missing:
    run_cmd(f'pip install --ignore-installed {" ".join([*missing])}')

# -------------------------------------------
    
import pkg_resources
import subprocess
import sys
import os

REQUIRED = {
  'spacy', 'scikit-learn', 'numpy', 'pandas', 'torch',
  'pyfunctional', 'textblob', 'seaborn', 'matplotlib'
}

installed = {pkg.key for pkg in pkg_resources.working_set}
missing = REQUIRED - installed

if missing:
    python = sys.executable
    subprocess.check_call([python, '-m', 'pip', 'install', *missing], stdout=subprocess.DEVNULL)
```