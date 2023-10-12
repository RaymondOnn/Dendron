---
id: vs314unbbuw0opiw271c1ki
title: yaml
desc: ''
updated: 1696701641177
created: 1696700277932
---


## Format: YAML vs Python

- Simplicity and readability: YAML is often considered more human-readable and easier to understand than Python code, especially for non-programmers.
- Flexibility and complexity: Python may be a better choice if you need to create a configuration file with advanced logic such as conditional statements, loops, and functions
- Security: YAML files can be a security risk if not properly sanitized, as they can contain arbitrary code execution.
- Integration with other systems: Depending on the other systems you’re working with, one format may be more compatible.

## Using YAML Config Files

#### YAML config file

```yaml

books:
  - name: "The Catcher in the Rye"
    description: "A story about a teenage boy named Holden Caulfield who is kicked out of his prep school and wanders around New York City."
    pages: 224
    genre: Fiction
  - name: "To Kill a Mockingbird"
    description: "A story about racial injustice in a small Alabama town during the 1930s."
    pages: 336
    genre: Fiction
  - name: "The Elements of Style"
    description: "A book about English language style and usage."
    pages: 105
    genre: Nonfiction
```

#### Python code to read yaml config file

``` py
import yaml

# Load the configuration file
with open('config.yml', 'r') as f:
    config = yaml.safe_load(f)
    
# Create a list of books
books = []
for book_config in config['books']:
    name = book_config['name']
    description = book_config['description']
    pages = book_config['pages']
    genre = book_config['genre']
    book = {'name': name, 'description': description, 'pages': pages, 'genre': genre}
    books.append(book)
```

### Using Dependency Injector to Load Configuration

``` py
from dependency_injector import containers, providers

class Book:
    def __init__(self, name, description, authors, pages):
        self.name = name
        self.description = description
        self.authors = authors
        self.pages = pages


class BookContainer(containers.DeclarativeContainer):
    config = providers.Configuration()
    book_provider = providers.Factory(
        Book,
        name=config.books[0]['name'],
        description=config.books[0]['description'],
        authors=config.books[0]['authors'],
        pages=config.books[0]['pages']
    )

if __name__ == '__main__':
    with open("books.yaml", "r") as f:
        books_data = yaml.safe_load(f)
    container = BookContainer(config={'books': books_data})
    book = container.book_provider()
    print(book.name)
    print(book.description)
    print(book.authors)
    print(book.pages)
```

### Watching Updates to YAML

- To test this code, you can modify and save the YAML file while the program is running.
- For example, you can add a new book to the YAML file

``` py
import yaml
import time
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
from dependency_injector import containers, providers


class Book:
    def __init__(self, name, description, authors, pages, version):
        self.name = name
        self.description = description
        self.authors = authors
        self.pages = pages
        self.version = version

        
class BookContainer(containers.DeclarativeContainer):
    config = providers.Configuration()
    book_provider = providers.Factory(
        Book,
        name=config.books[0]['name'],
        description=config.books[0]['description'],
        authors=config.books[0]['authors'],
        pages=config.books[0]['pages'],
        version=config.version
    )

    
# loads the YAML configuration and extracts the version number. 
def load_config():
    with open("books.yaml", "r") as f:
        books_data = yaml.safe_load(f)
        version = books_data.pop("version", None)
        return (books_data, version)

        
# creates a new BookContainer instance with the updated configuration and returns it. 
def update_config():
    config, version = load_config()
    container = BookContainer(config={'books': config, 'version': version})
    return container

    
# watches for changes to the YAML file using the watchdog library 
# updates the class instances whenever the file is modified. 
class ConfigEventHandler(FileSystemEventHandler):
    def __init__(self):
        self._observer = Observer()
        self._observer.schedule(self, ".", recursive=False)
    
    def on_modified(self, event):
        if event.src_path.endswith(".yaml"):
            print("Configuration file modified")
            container = update_config()
            # update all the book instances with new version
            for book in container.book_provider.all_objects:
                book.version = container.config.version

                
# starts the file watcher loop.
def start_config_watcher():
    event_handler = ConfigEventHandler()
    event_handler._observer.start()
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        event_handler._observer.stop()
    event_handler._observer.join()

    
if __name__ == '__main__':
    container = update_config()
    for book in container.book_provider.all_objects:
        print(f"{book.name}: {book.version}")
    start_config_watcher()
```
