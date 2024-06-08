---
id: j4xuccg0lrj7h2xg7dw7l36
title: yaml
desc: ''
updated: 1713557810406
created: 1694602245194
---
https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started
https://stackoverflow.com/questions/3790454/how-do-i-break-a-string-in-yaml-over-multiple-lines
## What is YAML

- a data serialization language i.e. standard format to transfer data e.g. XML, JSON
- made up of key-value pairs

## Why YAML?

- Human readable and intuitive
- Based on line separation and indentation


### Environment Variables

- Use environment variables by using the $ prefix with the variable name
- E.g. to use the environment variable `VARIABLE`, use `$VARIABLE`

### Placeholders (<small>`{{ }}`</small>)
- Used to reference variables inside YAML files
- Avoids hardcoded values
- Helm uses these for templating Kubernetes YAML files and replaces these with real values using the Go Templating Engine

``` yaml
#values.yaml
image:
    name: nginx
    tag: latest

---

apiVersion: v1
kind: Service
metadata: 
    name: {{ .Values.service.name }}
spec:
    selector:
        app: {{ .Values.service.app }}
    ports:
        - protocol: TCP
          port: {{ .Values.service.port }}
          targetPort: {{ .Values.service.targetport }}
```

### Separating Multiple YAML documents

- We can indicate the boundaries of a yaml file with `---` symbol
- Multiple YAML files can be combined into a single file by separating them with `---`.

## Working with files

### Writing to file

The `dump()` method serializes a python object into a YAML stream

``` py
import yaml

users = [
    {'name': 'John Doe', 'occupation': 'gardener'},
    {'name': 'Lucy Black', 'occupation': 'teacher'}
]

with open('test.yaml', 'w') as fL
    data = yaml.dump(users, f)
```
