---
id: b60t2ue4xjpk40wnrbe5r57
title: basics
desc: ''
updated: 1697103500933
created: 1697083410763
---
<https://www.youtube.com/watch?v=GOk4IoYhM9U&ab_channel=DevOpsMadeEasy>


### Basic Data Types

#### Strings

- quotes are optional unless special characters used i.e. "\n"
- Escape sequences are evaluated if enclosed in quotes

###### Multi Line Strings

- fold (`>`): Does not interpret newlines
- block (`|`): Interprets newlines
- To remove the whitespace after the string, use the strip operator `-`
  - fold (`>-`)
  - block (`|-`)
  
``` yaml
multiLineString: |
    this is a multiline string
    and this is the next line
veryLongSingleString: >
    this is a very long single line string
    that is split into many lines
    and should be all on one line    
```

#### Booleans

- yes/no, on/off, true/false all work

```yaml
app: user-authentication 
port: 9000
version: 1.7
deployed: true 
```

#### Null Values (~)

``` yaml
# In python, interpreted as None
name: ~
age: null
```

### Complex Data Types

#### Dictionaries

```yaml
microservices:
    app: user-authentication 
    port: 9000
    version: 1.7
```

#### Lists

``` yaml
microservices:
    - app: user-authentication 
      port: 9000
      version: 1.7
    - app: shopping-cart
      port: 9002
      version: 
        - 1.9
        - 2.0
        - 2.1

 # This works too    
      version: [1.9, 2.0, 2.1]   
      
microservices:
    - user authentication
    - shipping
```

- Each 'bullet' indicates the start of a list element
- Block Style: Uses spaces for structuring the document. Easier to read but less compact

  ``` yaml
  color:
    - red
    - yellow
    - green
  ```

- Flow Style: Uses inline way of structing the document without using indentation. '[]' for sequences and '{}' for mappings.

  ``` yaml
  color: [red, yellow, green]
  ```

#### Sets

- Duplicate values are removed automatically
- Need to indicate that the data type is a set using `!!set` after the set name
- Set items are preceded by the question mark (`?`) like list items are preceded by hyphens (`-`)

``` yaml
set_elements: !!set
    ? 1
    ? 2
    ? 2
    ? 3
    ? 4
    ? 3
    
set_elements: {1, 2, 'test', 'yaml'}
```
