---
id: s6pbkf29bt83ikic8t3yyul
title: Repository
desc: ''
updated: 1698137805403
created: 1697131907538
---

<https://medium.com/python-in-plain-english/repository-pattern-is-insane-if-you-know-how-to-use-it-properly-python-88a05f03a50c>

## Repository Pattern

### What is Repository Pattern

- The main idea of this pattern is to abstract the underlying data persistence code.
- This is great because it allows easier testing and maintenance.
- Besides that it provides a nice interface with, most commonly, 5 methods that can do anything you need.

### Why Repository Pattern

- Allows you to create an abstraction layer between data access logic and business logic, which will create decoupling.
- This means that your app won’t care about the specifics of data source / data storage mechanism.
- That way your code become more maintainable because for any changes to the persistence layer (repository), you make the change in only one place independent of the business logic

### Example: Repository of Books objects

#### Project structure:
``` md
├── domain
│   ├── __init__.py
│   └── book.py
├── main.py
└── repository
    ├── __init__.py
    └── interface.py
```
#### domain/book.py
``` py
class Book:
    def __init__(
        self,
        book_id: str,
        title: str,
        author: str,
        pages: int
    ):
        self.id = book_id
        self.title = title
        self.author = author
        self.pages = pages

    def __str__(self):
        return (
            f"{self.id} - "
            f"{self.title} by "
            f"{self.author}, "
            f"{self.pages}\n"
        )
```
#### repository/interface.py
This is the interface of our repository

``` py
from typing import Protocol
from typing import List
from domain.book import Book


class IRepository(Protocol):
    """ Repository for books """

    def get(self, book_id: str) -> Book:
        """ Get a specific book by id """
        raise NotImplementedError("get not implemented")

    def get_all(self) -> List[Book]:
        """ Get all books """
        raise NotImplementedError("get_all not implemented")

    def save(self, book: Book) -> None:
        """ Store a book """
        raise NotImplementedError("save not implemented")

    def update(self, book_id: str, new_book: Book) -> Book:
        """ Update a book """
        raise NotImplementedError("update not implemented")

    def delete(self, book_id: str) -> Book:
        """ Delete a book """
        raise NotImplementedError("delete not implemented")
```
#### repository/base.py
We will use dictionaries to store books in memory, because they are implemented using hash tables which they give us an average complexity of O(1).
```py
from typing import List
from .interface import IRepository
from domain.book import Book


class Repository(IRepository):
    def __init__(self):
        self._books = {}  # Our Memory Database

    def get(self, book_id: str) -> Book:
        """ Get a specific book by id """

        return self._books.get(book_id)

    def get_all(self) -> List[Book]:
        """ Get all books """

        return list(self._books.values())

    def save(self, book: Book) -> None:
        """ Store a book """

        if book.id in self._books:
            raise ValueError(
                f"Book with id {book.id} already exists!"
            )

        self._books[book.id] = book

    def update(self, book_id: str, book: Book) -> Book:
        """ Update a book """

        if book_id not in self._books:
            raise ValueError(
                f"Bookd with id {book_id} does not exist!"
            )

        self._books[book_id] = book

        return self._books[book_id]

    def delete(self, book_id: str) -> Book:
        """ Delete a book """

        if book_id not in self._books:
            raise ValueError(
                f"Book with id {book_id} does not exist!"
            )

        removed_book = self._books[book_id]
        del self._books[book_id]

        return removed_book
```

#### main.py
``` py
from domain.book import Book
from repository.base import Repository


def main():
    # Create the repository
    repo = Repository()

    # Create a book
    book = Book("1", "Clean Code", "Robert C. Martin", 464)

    # Save the book
    repo.save(book)

    # Get the book
    print(repo.get("1"))
    # 1 - Clean Code by Robert C. Martin, 464

    print(repo.get_all())
    # It will return us a list with all the books
    # [1 - Clean Code by Robert C. Martin, 464]

    new_book = Book("1", "Updated Book", "New author", 42)

    repo.update("1", new_book)
    # It will return us the book that was updated, but we don't print it
    # 1 - Updated Book by New author, 42

    print(repo.delete("1"))
    # It will return us the book that was deleted
    # 1 - Updated Book by New author, 42

    print(repo.get("1"))
    # None


if __name__ == "__main__":
    main()
>>> 1 - Clean Code by Robert C. Martin, 464
>>> [<domain.book.Book object at 0x10e8f8550>] # List of all books
>>> 1 - Updated Book by New author, 42
>>> None
```