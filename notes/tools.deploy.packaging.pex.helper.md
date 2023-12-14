---
id: 2g1qh3ewnk111i5w23l0va8
title: helper
desc: ''
updated: 1699577476295
created: 1699577395650
---

``` py
import json
import subprocess
import sys

from pex.fetcher import PyPIFetcher
from pex.pex_builder import PEXBuilder
from pex.resolvable import Resolvable
from pex.resolver import resolve_multi, Unsatisfiable, Untranslateable
from pex.resolver_options import ResolverOptionsBuilder


def pack(pex_file):
    current_packages = {package["name"]: package["version"]
                        for package in _get_packages(False)}
    requirements_to_install = [name + "==" + version
                               for name, version in current_packages.items()]
    resolver_option_builder = ResolverOptionsBuilder(
        use_manylinux=True,
        fetchers=[PyPIFetcher(), PyPIFetcher()])
    resolvables = [Resolvable.get(req, resolver_option_builder) for
               req in requirements_to_install]
    pex_builder = PEXBuilder(copy=True)

    try:
        resolveds = resolve_multi(resolvables, use_manylinux=True)
        for resolved in resolveds:
            print("Add requirement %s", resolved.distribution)
            pex_builder.add_distribution(resolved.distribution)
            pex_builder.add_requirement(resolved.requirement)
    except (Unsatisfiable, Untranslateable):
        raise

    pex_builder.build(pex_file)


def _get_packages(editable):
    editable_mode = "-e" if editable else "--exclude-editable"
    results = subprocess.check_output(
        [f"{sys.executable}", "-m", "pip", "list", "-l",
         f"{editable_mode}", "--format", "json"]).decode()

    parsed_results = json.loads(results)

    # https://pip.pypa.io/en/stable/reference/pip_freeze/?highlight=freeze#cmdoption--all
    # freeze hardcodes to ignore those packages: wheel, distribute, pip, setuptools
    # To be iso with freeze we also remove those packages
    return [element for element in parsed_results
            if element["name"] not in
            ["distribute", "wheel", "pip", "setuptools"]]
```
