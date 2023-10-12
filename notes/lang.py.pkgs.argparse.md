---
id: 94o3kx3leezqfpvyvao1aqu
title: Argparse
desc: ''
updated: 1695076263293
created: 1694831883665
---
https://www.youtube.com/watch?v=idq6rTYqvkY&ab_channel=FabioMusanni-ProgrammingChannel

## [Why do we need CLI functionality?](https://dev.to/thefern/command-line-utility-with-python-2plj)

- Makes python programs configurable
- Allows non-programmers to change according to their needs

## Approaching CLI Design
- Functions are divided by responsibility
- Menu
- Configuration
- Error log
- Exception handling
- Feedback for the user (Progress bar, and results)
- Customization through settings.ini

## Adding Arguments
``` py
# program.py
import argparse

parser = argparse.ArgumentParser(
    description='A random program that does something',
    add_help=True
)

# adding positional arguments
parser.add_argument('filename')

args = parser.parse_args()
print(args)
```
Running via CLI

```bash
python python.py -h
>>> A random program that does something 
```