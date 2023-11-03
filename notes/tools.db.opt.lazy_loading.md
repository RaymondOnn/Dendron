---
id: ooa77rk1ipqsn7ae6v5azd0
title: lazy_loading
desc: ''
updated: 1696699984721
created: 1696699182791
---
<https://code.likeagirl.io/understanding-lazy-loading-in-databases-with-python-and-sqlalchemy-e6e2067cfd8c>

## What is Lazy loading

- Lazy loading is a database optimization technique used to improve the efficiency of data retrieval by **loading data from the database only when it’s explicitly requested**.
- It revolves around delaying data retrieval until necessary, reducing unnecessary database queries and optimizing resource usage.
- This approach is precious when working with large datasets or complex table relationships.

### Benefits of Lazy Loading

Lazy loading offers several advantages:

- Efficient Resource Utilization: Lazy loading minimizes the amount of data transferred between the application and the database, conserving network resources and reducing query execution time.
- Improved Responsiveness: Applications remain responsive even when dealing with extensive datasets, as data is fetched on the fly, reducing the initial loading time.
- Memory Optimization: By loading data incrementally, lazy loading prevents memory overconsumption, making it suitable for applications with limited resources.

### Implementation (with `SQLAlchemy`)

Let’s explore how to implement lazy loading using Python and SQLAlchemy:

#### Defining Your Data Model

``` PY
# Suppose you have two tables: Author and Book
# An author can have multiple books. 

from sqlalchemy import create_engine, Column, Integer, String, ForeignKey
from sqlalchemy.orm import relationship, sessionmaker
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class Author(Base):
    __tablename__ = 'authors'
    id = Column(Integer, primary_key=True)
    name = Column(String)
    # Relationship with Book defined with lazy='dynamic'. 
    # Hence books associated with an author will be retrieved lazily
    books = relationship('Book', lazy='dynamic')

class Book(Base):
    __tablename__ = 'books'
    id = Column(Integer, primary_key=True)
    title = Column(String)
    author_id = Column(Integer, ForeignKey('authors.id'))
```

#### Querying with Lazy Loading

Now, let’s use lazy loading to retrieve an author’s books:
``` py
# Create a session and query
engine = create_engine('sqlite:///library.db')
Session = sessionmaker(bind=engine)
session = Session()

# Query the author by name
author_name = "J.K. Rowling"
author = session.query(Author).filter(Author.name == author_name).first()

# Access the author's books (lazy loading)
books = author.books.all()  # This triggers the query to fetch the books

# Print the books
for book in books:
    print(f"{author_name} wrote {book.title}")
```
