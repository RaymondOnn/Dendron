---
id: 82rx154pju7irkqzxjrepsx
title: sqlalchemy
desc: ''
updated: 1705824648198
created: 1696701828060
---

> #### Links
>
> -   https://code.likeagirl.io/using-data-classes-to-create-database-models-in-python-b936301aa4ad
> -   https://medium.com/technology-hits/inverting-dependencies-between-an-orm-and-model-classes-using-sql-alchemy-in-python-design-patterns-5e927c5d858

- https://auth0.com/blog/sqlalchemy-orm-tutorial-for-python-developers/#SQLAlchemy-Introduction

### What is SQLAlchemy?

-   SQLAlchemy is an open-source SQL toolkit and object-relational mapper(ORM) for Python.
-   It facilitates communication between Python programs and databases. Most of the time, this library is used as an ORM tool.
-   To install: `pip install sqlalchemy`

> #### What is ORM?
>
> -   Object Relational Mapper(ORM) is the specialization of the Data Mapper design pattern that addresses relational databases like MySQL, Oracle, and PostgreSQL.
>     -   Mappers move data between objects and a database while keeping them independent of each other. 
>     - The problem is that OOP languages and RDBMS structure data differently (OOP: objects, RDVMS: database records), hence a solution is needed (which a ORM provides) to translate from one schema to the other.
>
> #### What are the pros and cons of ORM?
>
> ##### Pros
>
> -   **Conciseness:** ORM allows users to use programming languages of their choice with more concise and simple syntax than using raw SQL queries, reducing the amount of code needed.
> -   **Optimal**: ORM also enables users to take advantage of the optimization of OOP such as inheritance, encapsulation, and abstraction by representing database records as objects.
> -   **Flexibility**: With ORM, users can easily switch between different database management systems (MySQL, Sqlite3, PostgreSQL,…) without having to possess a sound understanding of these systems as well as SQL language.
>
> ##### Cons
>
> -   Time: Because ORMs are frameworks, users would have to spend time learning and familiarizing themselves with these tools.
> -   Less control: Using ORM, users will have less control and initiative with databases.

### Fundamental Concepts
#### DataBase API (DBAPI)

-   e.g. psycopg (Postgres)
-   The DBAPI specifies how Python modules that integrate with databases should expose their interfaces.
-   defines how common functions like `connect`, `close`, `commit`, and `rollback` must behave.
-   Consequently, whenever we use a Python module that adheres to the specification, we can rest assured that we will find these functions and that they will behave as expected.
-

#### Connection Pools

-   Connection pooling is an implementations of the [[concepts.software_design.oop.design_patterns.object_pool]] pattern to improve performance.
    -   Opening and maintaining new database connections is expensive and wastes resources.
    -   Also allows easier management of the number of connections that an application might use simultaneously.
    -   Other examples include the `create_engine()` function which generates a QueuePool.
-   Some of the most common configuration options include:
    -   **pool_size:** Sets the number of connections that the pool will handle.
    -   **max_overflow**: Specifies how many exceeding connections (relative to pool_size) the pool supports.
    -   **pool_recycle**: Configures the maximum age (in seconds) of connections in the pool.
    -   **pool_timeout**: Identifies how many seconds the program will wait before giving up on getting a connection from the pool.

#### Dialects

-   Most of the popular relational databases available out there adhere to the SQL standard, but introduce proprietary variations, resulting in the existence of dialects.

    -   For e.g., to fetch the first ten rows of a table called people.

        ```sql
        -- Microsoft SQL Server:
        SELECT TOP 10 \* FROM people;

        -- MySQL
        SELECT * FROM people LIMIT 10;
        ```

-   To issue the right query, the dialect used by the database needs to be considered
-   By default, these dialects are included: Firebird, Microsoft SQL Server, MySQL, Oracle, PostgreSQL, SQLite, Sybase
-   Dialects for other database engines, like Amazon Redshift, are supported as external projects but can be easily installed.

#### Engines

-   The Engine is the starting point for any SQLAlchemy application.
-   Engines are references Pools and Dialects to interact with DBAPI functions.
-   Most common database management systems available are supported for e.g. PostgreSQL, MySQL, Oracle, Microsoft SQL Server, SQLite
-   To create an engine:

    ```py
    from sqlalchemy import create_engine
    engine = create_engine('postgresql://usr:pass@localhost:5432/sqlalchemy')
    ```

    -   **Note**:
        -   Creating an engine does not connect to the database instantly.
        -   Connection happens when needed until `engine.connect()` is called, or a query is submitted via `engine.execute()`

### Working with SQLAlchemy 
```py
from sqlalchemy import create_engine, Column, Integer, String, DateTime
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

engine = create_engine('sqlite:///your_database_name.db')

class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    username = Column(String(50), unique=True, nullable=False)
    email = Column(String(100), unique=True, nullable=False
    password = Column(String(100), unique=True, nullable=False)
    created_at = Column(DateTime, default=datetime.datetime.utcnow)

# Actually creating the users table
Base.metadata.create_all(engine)

# -----------------------------------

Session = sessionmaker(bind=engine)
session = Session()

# Example: Inserting a new user into the database
new_user = User(username='Sandy', email='sandy@gmail.com', password='cool-password')
session.add(new_user)
session.commit()

# Example: Querying all users from the database
all_users = session.query(User).all()

# Example: Querying a specific user by their username
user = session.query(User).filter_by(username='Sandy').first()

session.close()
```


#### Declarative Base

- A `declarative_base` is a combination of a metadata container for tables and a set of methods that act as mappers between Python and SQL databases. 
- By inheriting the classes we create from the `declarative_base` object, which is called `Base`, we can avoid the need to rewrite code.
``` py 
# a class that represents a database table using the Declarative Base:

from sqlalchemy.ext.declarative import declarative_base  # returns Base class

Base = declarative_base()

class User(Base):
    __tablename__ = 'users' # associate User class with "users" table
    
    # each attribute corresponds to a table column
    id = Column(Integer, primary_key=True)
    username = Column(String(50), unique=True)
    email = Column(String(100))
    
    def __init__(self, username, email):
        self.username = username
        self.email = email
```
#### [Metadata](https://www.geeksforgeeks.org/describing-databases-with-metadata-sqlalchemy/)
- Metadata in SQLAlchemy serves as a catalog storing database information i.e. tables, columns, and indexes so that we don't need to keep looking them up.
- Allows SQLAlchemy to be awareness of the database's structure and schema.
- Metadata is an object which consists of Table objects keyed to their string names
- A single MetaData object is enough for an entire application.
``` py
# convert table into schema using the Metadata Object

from sqlalchemy import MetaData
from sqlalchemy import Integer, String, Column, Table
 
metadata_object=MetaData()
 
student_table = Table(
     "student_account",
     metadata_object,
     Column('id', Integer, primary_key=True),
     Column('name', String(30)),
     Column('age',Integer),
     Column('grade', String(80))
)

# creating an engine object
engine = create_engine("sqlite+pysqlite:///:memory:",
                       echo=True, future=True)
 
# emitting DDL
metadata_object.create_all(engine)
```
###### [Reflection](https://betterprogramming.pub/reflecting-postgresql-databases-using-python-and-sqlalchemy-48b50870d40f)
- Reflection is a technique that allows us to populate a SQLAlchemy object from an existing database. 
- You can reflect tables, views, indexes, and foreign keys. 
``` py
# binding a MetaData object to the database engine:
from sqlalchemy import MetaData, create_engine
metadata = MetaData()
engine = create_engine('sqlite:///Chinook_Sqlite.sqlite') 1
```

#### Sessionmaker
- In SQLAlchemy, sessions are used to interact with the database. 
- Each session contains an identity map i.e. an empty dict with keys for the table name, columns, and primary keys. 
- When the session fetches data, it populates the identity map and uses it to create an object with specific attribute values. 
- Conversely, when data is committed to the database, the identity map is filled in the same manner, but the resulting data is unpacked into a row in the students table.

``` py
from sqlalchemy.orm import sessionmaker

# bind database engine to Session object
Session = sessionmaker(bind=engine)

# init instance of Session object
session = Session()

```


---
```
 # Declare a ``FetchTable`` class for session-based queries.
    class FetchTable(Base):
        __table__ = Table(table, metadata, autoload=True, autoload_with=ds)
    
        def get_column(self, \
                       col=None):
            # Retrieve a ``Column`` object for this ``table``.
            return self.__table__.columns[col]
```