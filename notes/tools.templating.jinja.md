---
id: nam9jp3u9ysmgb6scegj7mb
title: jinja
desc: ''
updated: 1702236350224
created: 1702177557352
---

## Jinja

### What is Jinja

-   Jinja is an incredibly powerful and flexible templating language for Python
-   Provides a syntax for creating dynamic web pages and templates.
-   It was originally created by Armin Ronacher in 2008 as a replacement for the outdated templating language used in the Flask web framework.
-   Jinja uses a set of special characters and keywords to denote variables, control structures, and other elements of a template.
    -   `{{ }}`, which enclose a variable
    -   `{% %}` for control structures like loops and conditionals.
    -   `{# #}` for comments
    -   `# ##` for line statements

### Python API
- We start with a simple example
  ```py
  #!/usr/bin/env python3
  from jinja2 import Template
  name = input("Enter your name: ")
  tm = Template("Hello {{ name }}")
  msg = tm.render(name=name)
  print(msg)
  ```
- We can work with objects in our template strings.

  ``` py
  objects.py
  #!/usr/bin/env python3
  from jinja2 import Template
  class Person:
      def __init__(self, name, age):
          self.name = name
          self.age = age
      def getAge(self):
          return self.age
      def getName(self):
          return self.name    
  person = Person('Peter', 34)
  tm = Template("My name is {{ per.getName() }} and I am {{ per.getAge() }}")
  msg = tm.render(per=person)
  print(msg)
  ```
- Jinja allows a convenient dot notation to access data in Python dictionaries.
  ``` py
  dicts.py

  #!/usr/bin/env python3
  from jinja2 import Template

  person = { 'name': 'Person', 'age': 34 }
  # Both the active and the commented way are valid. The dot notation is more convenient.
  tm = Template("My name is {{ per.name }} and I am {{ per.age }}")
  # tm = Template("My name is {{ per['name'] }} and I am {{ per['age'] }}")
  msg = tm.render(per=person)
  print(msg)
  ```
- We can use `raw`, `endraw` markers to escape Jinja delimiters.
  - By using the `raw`, `endraw` block, we escape the Jinja `{{ }}` syntax. It is printed in its literal meaning.
  ``` py
  raw_data.py

  #!/usr/bin/env python3
  from jinja2 import Template
  data = '''
  {% raw %}
  His name is {{ name }}
  {% endraw %}
  '''
  tm = Template(data)
  msg = tm.render(name='Peter')
  print(msg)
  ```
- To escape data such as < or > characters, we can use a filter or the escape() function.
  ``` py
  escape_data.py

  #!/usr/bin/env python3
  from jinja2 import Template, escape
  data = '<a>Today is a sunny day</a>'
  tm = Template("{{ data | e}}") # Using the e filter, the data is escaped
  msg = tm.render(data=data)
  print(msg)
  print(escape(data)) # escape has same functionality as the e filter
  ```
- Here's how to work with template files
The for expression is used to iterate over a data collection in a template.

Now we do not use a simple string template anymore. We use a text file which is loaded with FileSystemLoader.
  ``` py
  for_expr.py

  #!/usr/bin/env python3
  from jinja2 import Environment, FileSystemLoader
  persons = [
      {'name': 'Andrej', 'age': 34}, 
      {'name': 'Mark', 'age': 17}, 
      {'name': 'Thomas', 'age': 44}, 
      {'name': 'Lucy', 'age': 14}, 
      {'name': 'Robert', 'age': 23}, 
      {'name': 'Dragomir', 'age': 54}
  ]
  file_loader = FileSystemLoader('templates')
  env = Environment(loader=file_loader)
  template = env.get_template('showpersons.txt')
  output = template.render(persons=persons)
  print(output)
  ```
  ``` txt
  # ./templates/showpersons.txt
  {% for person in persons -%}
      {{ person.name }} {{ person.age }}
  {% endfor %}

  ```


```py
from jinja2 import Environment, FileSystemLoader

template_dir = os.path.dirname(os.path.abspath(args.template))
environment = Environment(loader=FileSystemLoader(template_dir))

template_file = os.path.basename(args.template)
template = environment.get_template(template_file)

content = template.render(data)
```

### Jinja Templating Basics

#### Comments: `{# #}`

-   Use the `{# #}` to add comments to your Jinja template.
-   Great for documenting your code or adding notes for other developers who might be working on the same project.

#### Variables

-   Used to insert dynamic content into a template.
-   You can define a variable using the `{% set %}` tag, and then use it in the template by enclosing it in double curly braces.
-   For e.g. if you define a variable “name” as “Jinja”, you can insert it into a sentence like “Welcome to {{ name }} templates!”. Let’s see it in action in a simple HTML code.


```py
{% set name = "Jinja" %}
<!DOCTYPE html>
<html>
  <head>
    <title>My Jinja Template</title>
  </head>
  <body>
    <h1>Welcome to {{ name }} templates!</h1>  # becomes 'Welcome to Jinja templates!
    <p>This is an example of how to use variables in a Jinja template.</p>
  </body>
</html>
```

#### Control Structures

-   Control structures allows for adding logic and conditional behavior and helps to customize the output of your template based on dynamic data.
-   Some of the control structures you’ll encounter include “if”, “for”, “else”, and “endif”.

```py
{% if user %}    #  checks if user is defined
  <h1>Hello, {{ user.username }}!</h1>
  {% if user.is_admin %} # checks if user is admin
    <p>Welcome, admin user!</p>
  {% else %}
    <p>Welcome, regular user!</p>
  {% endif %} # <-- end of if-else statement
  <ul>
    {% for item in items %}
      <li>{{ item }}</li>
    {% endfor %} # <-- end of for loop
  </ul>
{% else %}
  <p>Please log in to continue.</p>
{% endif %}
```

#### Filters

-   Filters allow you to modify the output of a variable before it’s inserted into the template. For e.g., convert a string to title case via `capitalize`` filter.
-   Filters are applied to the variable using the pipe (|) character, followed by the filter name.

```py
{% set my_string = "hello, world!" %}

<p>Original string: {{ my_string }}</p>

<p>Capitalized string: {{ my_string|capitalize }}</p> # Hello, World!
```

-   Here’s a list of some commonly used filters in Jinja:
    -   `abs`: Returns the absolute value of a number.
    -   `capitalize`: Converts the first character of a string to uppercase and the rest to lowercase.
    -   `default`: Returns a default value if a variable is undefined or empty.
    -   `escape`: Escapes HTML characters in a string to prevent XSS attacks.
    -   `float`: Converts a variable to a floating-point number.
    -   `int`: Converts a variable to an integer.
    -   `join`: Joins a list of strings into a single string, separated by a specified delimiter.
    -   `length`: Returns the length of a string or list.
    -   `lower`: Converts a string to lowercase.
    -   `round`: Rounds a number to a specified number of decimal places.
    -   `slice`: Extracts a slice of a list or string.
    -   `title`: Converts a string to title case.
    -   `upper`: Converts a string to uppercase.

#### Macros

-   Macros are reusable snippets of code that can be called from within a template. This can be helpful for reducing code duplication and improving readability.
-   Macros are defined using the {% macro %} and {% endmacro %} tags, and can be called from within the template using the macro name and passing in any necessary parameters.

```py
{% macro print_name(name) %} # macro acts like functions
    <p>Hello, {{ name }}!</p>
{% endmacro %}

{{ print_name("John") }} # Hello, John!
{{ print_name("Jane") }} # Hello, Jane!
{{ print_name("Bob") }} # Hello, Bob!
```

### Advanced Jinja Templating Techniques

#### Template Inheritance

-   Template inheritance allows you to create a base template that can be extended by child templates.
-   Useful for creating consistent layouts across multiple pages of a website or application.

##### Include statement

-   The Include statement allows you to reuse code across multiple templates.
-   By defining reusable blocks of code in separate files, you can simplify your templates and make them easier to maintain.
-   For e.g. Suppose you have a Jinja template file called index.html that contains the basic structure of your website. You want to include a header file that contains the header section of your website.

```py
# index.html

<!DOCTYPE html>
<html>
  <head>
    <title>My Website</title>
  </head>
  <body>
    {% include 'header.html' %} # include the contents from header.html here
    <main>
      <h1>Welcome to My Website!</h1>
      <p>This is some content for my website.</p>
    </main>
  </body>
</html>
```

```py
# header.html

<header>
  <h1>My Website</h1>
  <nav>
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="/about">About</a></li>
      <li><a href="/contact">Contact</a></li>
    </ul>
  </nav>
</header>
```

##### Extends statement

-   The Extends statement is a variation of the Include statement that allows you to extend the functionality of a base template.
-   With this statement, you can override specific blocks of code in the base template while keeping the rest of the template intact.
-   This approach is especially useful for creating custom themes or designs for a website or application.

```py
# base_template.html:

<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}{% endblock %}</title> # <-- title supplied via chile template
</head>
<body>
    <header>
        {% block header %}
        <nav>
            <ul>
                <li><a href="/">Home</a></li>
                <li><a href="/about">About</a></li>
                <li><a href="/contact">Contact</a></li>
            </ul>
        </nav>
        {% endblock %}
    </header>
    <main>
        {% block content %}{% endblock %}  # <-- content supplied via chile template
    </main>
    <footer>
        {% block footer %}
        <p>&copy; 2023 My Company</p>
        {% endblock %}
    </footer>
</body>
</html>
```

###### The child template

-   `{% extends %}` is used to specify which parent template to extend
-   `{% block %}` is used to define new blocks.
-   The blocks defined in the child template will override the corresponding blocks in the parent template.

```py
# child_template.html:

{% extends "base_template.html" %}

{% block title %}{{ title }}{% endblock %}

{% block content %}
    <h1>{{ title }}</h1>
    <p>{{ content }}</p>
{% endblock %}
```

### Jinja Best Practices

#### Code Reusability

-   Macros and include statements can help increase code reusability

#### Performance Optimization

-   Avoid unnecessary computations: Only calculate variables that are actually needed in the template. Don’t perform expensive computations in the template that could be done beforehand in the Python code.
-   Embedding complex logic in templates — consider encapsulating complex logic in functions or methods in your application code instead, and then calling these functions from within your templates.
-   Use caching: If your template is static and doesn’t change often, you can use caching to store the rendered output in memory. This can greatly reduce the time it takes to render the template.

#### Error Handling

-   Use try-except blocks: Wrap any potentially problematic code in a try-except block to catch any errors and handle them appropriately.
-   Use conditional statements: Use if statements to check if a variable or value exists before trying to use it in the template.
-   Use default values: Set default values for variables that might not be defined in the template to avoid errors.

#### Security Considerations

-   Avoid using user input in templates: Never include user input directly in a template. Instead, use a framework like Flask or Django to handle user input and sanitize it appropriately.
-   Use safe filters: Jinja provides a “safe” filter that can be used to mark a string as safe and prevent Jinja from escaping any HTML or JavaScript code within it. Use this filter only when absolutely necessary and make sure the content is trusted.
-   Keep templates separate from Python code: Jinja templates should be kept separate from your Python code to avoid any potential security vulnerabilities.

#### Others

-   Separation of concerns: Keep the presentation layer separate from the logic layer. For e.g. avoid using database queries or complex business logic directly in your templates.
