---
id: 2hv27w29r14gwws0ngccyog
title: CLI
desc: ''
updated: 1751214708130
created: 1751203967558
---
### Plugin based CLI App

#### Project Structure

```md
mycli/
├── cli.py
├── myplugins/
│   ├── __init__.py
│   └── hello.py
├── pyproject.toml
```

#### `myplugins/hello.py`

```py
def hello_plugin(name: str):
    """Greets the user with the given name."""
    print(f"Hello, {name}!")
```

#### `pyproject.toml`

``` toml
[project]
name = "mycli"
version = "0.1"
dependencies = ["pluggy"]

[project.entry-points."mycli.plugins"]
hello = "myplugins.hello:hello_plugin"
```

- install in editable mode: `pip install -e .`

#### Update your cli.py to run plugins

- To actually allow selecting and running plugins:

```py
import importlib.metadata
import argparse
import inspect
import pluggy

def run_plugin(plugin_func):
    sig = inspect.signature(plugin_func)
    parser = argparse.ArgumentParser(description=plugin_func.__doc__)
    for name, param in sig.parameters.items():
        parser.add_argument(f"--{name}", required=True)
    args = parser.parse_args()
    bound = {k: getattr(args, k) for k in sig.parameters}
    plugin_func(**bound)

pm = pluggy.PluginManager("mycli")
for ep in importlib.metadata.entry_points(group="mycli.plugins"):
    pm.register(ep.load(), name=ep.name)

# Main entrypoint
if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("command", help="The command to run")
    args, remaining = parser.parse_known_args()

    plugin = pm.get_plugin(args.command)
    if plugin is None:
        print("Unknown command. Available commands:")
        for name, _ in pm.list_name_plugin():
            print(f" - {name}")
    else:
        run_plugin(plugin)
```

#### Running your CLI

```bash
python cli.py hello --name=Raymond

# Output:
Hello, Raymond!
```

#### Manually register the plugins to avoid the editable installs

``` py
import argparse
import inspect
import pluggy

# Plugin function imports — direct!
from mycliapp.myplugins.hello import hello
from mycliapp.myplugins.goodbye import goodbye  # Add more as needed

def run_plugin(plugin_func):
    sig = inspect.signature(plugin_func)
    parser = argparse.ArgumentParser(description=plugin_func.__doc__)
    for name, param in sig.parameters.items():
        parser.add_argument(f"--{name}", required=True)
    args = parser.parse_args()
    bound = {k: getattr(args, k) for k in sig.parameters}
    plugin_func(**bound)

def main():
    pm = pluggy.PluginManager("mycli")
    # Direct plugin registration — no install needed
    pm.register(hello, name="hello")
    pm.register(goodbye, name="goodbye")

    parser = argparse.ArgumentParser(description="My CLI App")
    parser.add_argument("command", help="Command to run")
    args, remaining = parser.parse_known_args()

    plugin = pm.get_plugin(args.command)
    if not plugin:
        print("Available commands:")
        for name, _ in pm.list_name_plugin():
            print(f" - {name}")
        return

    run_plugin(plugin)

if __name__ == "__main__":
    main()
```

- Then just run from project root:

    ```bash
    python -m mycliapp hello --name Alice
    ```

#### Adding Global Arguments

``` py
import argparse
import inspect
import pluggy

# Plugins — import directly for private use
from mycliapp.myplugins.hello import hello
from mycliapp.myplugins.goodbye import goodbye

# You can store globals like this
global_context = {}

def run_plugin(plugin_func, remaining_args):
    sig = inspect.signature(plugin_func)
    parser = argparse.ArgumentParser(description=plugin_func.__doc__)
    for name, param in sig.parameters.items():
        parser.add_argument(f"--{name}", required=True)
    args = parser.parse_args(remaining_args)

    bound = {k: getattr(args, k) for k in sig.parameters}
    plugin_func(**bound)

def main():
    # Top-level parser with global args
    parser = argparse.ArgumentParser(description="My CLI App")
    parser.add_argument("--config", help="Path to config file")
    parser.add_argument("--verbose", action="store_true", help="Enable verbose logging")
    parser.add_argument("command", help="The command to run")
    args, remaining = parser.parse_known_args()

    # Store or use global options
    global_context["config"] = args.config
    global_context["verbose"] = args.verbose

    pm = pluggy.PluginManager("mycli")
    pm.register(hello, name="hello")
    pm.register(goodbye, name="goodbye")

    plugin = pm.get_plugin(args.command)
    if not plugin:
        print("Available commands:")
        for name, _ in pm.list_name_plugin():
            print(f" - {name}")
        return

    # Pass remaining args (after global ones) to plugin parser
    run_plugin(plugin, remaining)

if __name__ == "__main__":
    main()
```

#### Adding other features

- Docker compose file style config file
- `--dry-run` flag
- env overrides
- auto-generated docs

```py
### 📁 File: mycliapp/plugin_runner.py

import argparse
import inspect
import os
import yaml
from typing import Any, Callable

APP_NAME = "mycliapp"

# Load plugin config from a YAML file. Supports optional per-profile blocks.
def load_plugin_config(path: str, command: str, profile: str = "default") -> dict:
    try:
        with open(path, "r") as f:
            full_config = yaml.safe_load(f) or {}
            section = full_config.get(command, {})
            if isinstance(section, dict) and profile in section:
                return section.get(profile, {})
            return section  # fallback if no profile nesting
    except FileNotFoundError:
        return {}

# Resolve plugin arguments from environment variables
# Example: export MYCLIAPP_HELLO_NAME="Alice"
def from_env(command: str, param: str) -> Any:
    return os.environ.get(f"{APP_NAME}_{command}_{param}".upper())

# Extract argument metadata from plugin function signature for documentation
def get_plugin_signature(plugin_func: Callable) -> dict:
    sig = inspect.signature(plugin_func)
    return {
        name: {
            "required": param.default is inspect.Parameter.empty,
            "default": None if param.default is inspect.Parameter.empty else param.default,
            "annotation": str(param.annotation),
        }
        for name, param in sig.parameters.items()
    }

# Core logic to resolve plugin arguments from CLI, config, or env
# Gives priority: CLI > config > ENV > default
# Injects `context` if expected

def resolve_plugin_args(sig, cli_args, config, env_prefix, context):
    args = {}
    for name in sig.parameters:
        if name == "context":
            args[name] = context
            continue
        if cli_args.get(name) is not None:
            args[name] = cli_args[name]
        elif name in config:
            args[name] = config[name]
        elif (env := os.environ.get(f"{env_prefix}_{name}".upper())) is not None:
            args[name] = env
        elif sig.parameters[name].default is inspect.Parameter.empty:
            raise ValueError(f"Missing required argument: {name}")
    return args

# Main function to run a plugin
# Handles dry-run, verbose debug output, argument resolution, and calling the plugin

def run_command(plugin_func, command, argv):
    sig = inspect.signature(plugin_func)
    parser = argparse.ArgumentParser(description=plugin_func.__doc__)

    # Global/common flags
    parser.add_argument("--config", help="Path to config file")
    parser.add_argument("--profile", default="default", help="Config profile name")
    parser.add_argument("--dry-run", action="store_true", help="Preview without executing")
    parser.add_argument("--verbose", action="store_true", help="Enable verbose output")

    # Plugin-specific flags from its signature
    for name, param in sig.parameters.items():
        if name == "context":
            continue
        help_text = f"(type: {param.annotation.__name__})"
        if param.default is not inspect.Parameter.empty:
            help_text += f" (default: {param.default})"
        parser.add_argument(f"--{name}", required=False, help=help_text)

    args = parser.parse_args(argv)

    # Create shared global context dict for dry-run, config path, verbose, etc.
    context = {
        "dry_run": args.dry_run,
        "verbose": args.verbose,
        "config_file": args.config,
        "profile": args.profile,
    }

    # Load YAML config (if any)
    config = load_plugin_config(args.config, command, args.profile) if args.config else {}

    # Merge args from CLI, config, env, and context
    final_args = resolve_plugin_args(sig, vars(args), config, f"{APP_NAME}_{command}", context)

    # Dry run: show what would be executed
    if args.dry_run:
        print(f"[DRY RUN] Would run `{command}` with:")
        for k, v in final_args.items():
            print(f"  {k} = {v}")
        return

    # Optional debug logging
    if args.verbose:
        print(f"[DEBUG] plugin: {command}")
        print(f"[DEBUG] config: {config}")
        print(f"[DEBUG] args: {final_args}")

    # Execute plugin function with resolved arguments
    plugin_func(**final_args)

# CLI: list available plugins and their short docstrings
def list_plugins(pm):
    print("Available commands:")
    for name, plugin in pm.list_name_plugin():
        print(f" - {name}: {plugin.__doc__}")

# CLI: show full argument signature for one plugin (like `--docs`)
def show_docs(pm, command):
    plugin = pm.get_plugin(command)
    if not plugin:
        print(f"No such command: {command}")
        return
    sig = get_plugin_signature(plugin)
    print(f"Documentation for `{command}`:")
    for k, info in sig.items():
        print(f"  --{k}: {info['annotation']} "
              f"{'(required)' if info['required'] else f'(default={info['default']})'}")


```

- example config:

    ```yaml
    hello:
        default:
            name: Alice
            greeting: "Hello"

        prod:
            name: Alice
            greeting: "Hello from prod"

    ```

- example plugin:

    ``` py
    # Plugin
    def hello(name: str, greeting: str = "Hello", context: dict = None):
        """Greets the user."""
        if context and context.get("verbose"):
            print(f"[DEBUG] Greeting {name} with '{greeting}'")

        if context and context.get("dry_run"):
            print(f"[DRY RUN] Would say: {greeting}, {name}!")
            return

        print(f"{greeting}, {name}!")
    ```

- example usage:

    ``` bash
    # Use config file
    python -m mycliapp hello --config mycli.yaml

    # Override with CLI
    python -m mycliapp hello --config mycli.yaml --name Susan

    # Preview only (dry run)
    python -m mycliapp hello --config mycli.yaml --dry-run

    # Use env variables
    export MYCLIAPP_HELLO_NAME="EnvUser"
    python -m mycliapp hello

    # View available commands
    python -m mycliapp --list

    # View command docs
    python -m mycliapp --docs hello
    ```
