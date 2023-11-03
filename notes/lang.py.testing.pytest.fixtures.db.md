---
id: j96pwqngfm8woqnjuz6qdtu
title: database
desc: ''
updated: 1698629130873
created: 1698627553856
---

### [Testing Database Interactions with Fixtures](https://medium.com/@geoffreykoh/fun-with-fixtures-for-database-applications-8253eaf1a6d)

#### Defining the entities

- In this system, each customer will be a user, and each user can have one or more accounts.
- At the same time, an account can belong to a single user, or it can be a joint account belonging to multiple users.
- Every time the user credits or debits into the account, a transaction will be created.

``` py
# models.py that contains all the SQLAlchemy models

# Third party imports
from sqlalchemy import Column, Integer, String, DateTime, Float, ForeignKey
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
from sqlalchemy.sql import func

Base = declarative_base()


class User(Base):
    """ Model class to represent a user """

    __tablename__ = 'user'

    id = Column(Integer, primary_key=True, autoincrement=True)
    username = Column(String, nullable=False, unique=True)
    created_time = Column(DateTime(timezone=True),
                          server_default=func.now())
    modified_time = Column(DateTime(timezone=True),
                           server_default=func.now(),
                           onupdate=func.now())

    # Relationship with other tables
    accounts = relationship('Account', secondary='user_account')

# end class User


class Account(Base):
    """ Account within the application to hold balance """

    __tablename__ = 'account'

    id = Column(Integer, primary_key=True, autoincrement=True)
    balance = Column(Float, nullable=False)
    created_time = Column(DateTime(timezone=True),
                          server_default=func.now())
    modified_time = Column(DateTime(timezone=True),
                           server_default=func.now(),
                           onupdate=func.now())

    # Relationship with other tables
    users = relationship('User', secondary='user_account')

# end class Account


class UserAccount(Base):
    """ The association table to link User and Account """

    __tablename__ = 'user_account'

    user_id = Column(Integer, ForeignKey('user.id'), primary_key=True)
    account_id = Column(Integer, ForeignKey('account.id'), primary_key=True)

    # Relationship
    user = relationship('User', backref='account_association')
    account = relationship('Account', backref='user_association')

# end class UserAccount


class Transaction(Base):
    """ Association to the transactions """

    __tablename__ = 'transaction'

    id = Column(Integer, primary_key=True, autoincrement=True)
    account_id = Column(Integer, ForeignKey('account.id'))
    user_id = Column(Integer, ForeignKey('user.id'), nullable=True)
    amount = Column(Float, nullable=False)
    transaction_type = Column(String, nullable=False)
    created_time = Column(DateTime(timezone=True),
                          server_default=func.now())
    modified_time = Column(DateTime(timezone=True),
                           server_default=func.now(),
                           onupdate=func.now())

    # Relationship
    account = relationship('Account', backref='transactions')
    user = relationship('User', backref='transactions')

# end class Transaction
```

#### Defining the operations

We next define all the operations that can be applied to the system through another module,

``` py
# core.py that contains some of the common business functions

# Standard imports
from typing import List

# Third party imports
from sqlalchemy.orm.session import Session
from sqlalchemy.orm.exc import NoResultFound

# Application imports
from models import Account, User, Transaction


def get_user(username: str, session: Session) -> User:
    """ Gets the user by username """

    try:
        user = session.query(User).filter(User.username == username).one()
        return user
    except NoResultFound:
        return None

# end get_user()


def get_accounts_by_user(username: str, session: Session) -> List[Account]:
    """ Retrieves the accounts given the username """

    user = get_user(username, session)
    accounts = list(session.query(Account).filter(
        Account.users.contains(user)).all())
    return accounts

# end get_accounts_by_user


def compute_balance(username: str, session: Session) -> float:
    """ Computes the balance based on the username """

    accounts = get_accounts_by_user(username, session)
    balance = sum([account.balance for account in accounts])
    return balance

# end compute_balance()


def debit(account_id: int, amount: float, session: Session) -> float:
    """ Debits amount into the account """

    try:
        account = session.query(Account).filter(Account.id == account_id).one()
        account.balance -= amount
        transaction = Transaction(account_id=account_id,
                                  amount=amount,
                                  transaction_type='DEBIT')
        session.add(transaction)
        session.commit()
        return account.balance
    except NoResultFound:
        return 0

# end debit()


def credit(account_id: int, amount: float, session: Session) -> float:
    """ Debits amount into the account """

    try:
        account = session.query(Account).filter(Account.id == account_id).one()
        account.balance += amount
        transaction = Transaction(account_id=account_id,
                                  amount=amount,
                                  transaction_type='CREDIT')
        session.add(transaction)
        session.commit()
        return account.balance
    except NoResultFound:
        return 0

# end credit()
```

Now, let’s get on to testing them.

### Scenario 1: Using in-memory SQLite

- Whilst most articles on testing lean heavily into using mock objects to mock away interaction with databases, the completeness of the test depends on the correctness of that interaction.
- We can imagine the case whereby an SQL statement, or a query forming expression having errors that can go undetected.
- One of the easiest way (and of course the main reason why we use SQLAlchemy) is to use the fixtures to create an in-memory version of the database and insert test data into it, similar to how we insert test data in the previous examples.

``` py

# Third party imports
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# Application imports
from core import get_user, get_accounts_by_user, compute_balance, debit
from models import Base, User, Account, UserAccount, Transaction


@pytest.fixture(scope='function')
def setup_database():

    engine = create_engine('sqlite://')
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)
    session = Session()
    yield session
    session.close()
    
# end setup_database()

@pytest.fixture(scope='function')
def dataset(setup_database):

    session = setup_database

    # Creates user
    john = User(username='john')
    mary = User(username='mary')
    session.add(john)
    session.add(mary)
    session.commit()

    # Creates account
    john_account = Account(balance=10.0)
    mary_account = Account(balance=5.0)
    joint_account = Account(balance=20.0)
    john.accounts.append(john_account)
    mary.accounts.append(mary_account)
    john.accounts.append(joint_account)
    mary.accounts.append(joint_account)
    session.add(john_account)
    session.add(mary_account)
    session.add(joint_account)
    session.commit()

    yield session

# end dataset_1


def test_database(dataset):

    # Gets the session from the fixture
    session = dataset

    # Do some basic checking
    assert len(session.query(User).all()) == 2
    assert len(session.query(Account).all()) == 3
    assert len(session.query(UserAccount).all()) == 4

    # Retrieves John and Mary
    john = get_user('john', session)
    mary = get_user('mary', session)

    # Checks their accounts
    assert len(get_accounts_by_user(john.username, session)) == 2
    assert len(get_accounts_by_user(mary.username, session)) == 2

    # Checks the balance
    assert compute_balance(john.username, session) == 30.0
    assert compute_balance(mary.username, session) == 25.0

    # Attemps to debit from the joint account, i.e index 1
    joint_account = get_accounts_by_user(john.username, session)[1]
    debit(joint_account.id, 10.0, session)
    assert compute_balance(john.username, session) == 20.0
    assert compute_balance(mary.username, session) == 15.0

# end test_database()
```

- A few things to note here:
  - One fixture to setup the database, and another to insert the test data.
  - Within the core functions, it is a good practice to not assume or set up a database connection. Rather we treat the connection (session in this case) as a reference that can be passed around amongst the business logic functions.
- Whilst this approach of testing is very straight forward, and does not require much external dependencies, it does suffer from a few shortfalls, namely:
  - If we use database specific features such as ENUM or JSONB data types, some of the operations cannot be tested.
  - Forcing our application structure such that it can be tested on SQLite prevents us from using the more advance features that some databases provide.

### Scenario 2: Testing with pytest-postgresql

- Many database fixture libraries that allow for one to temporarily spin up the database of choice (provided you have the required executable).
- Most of them share similar interface signatures.

Here we are going to use pytest-postgresql, which is an pytest plugin, to set up the test database.

We need to install a working version of pg_ctl working. One of the easiest way is to use Anaconda (<https://anaconda.org>), like so:

``` sh
conda install -c anaconda postgresql
```

``` py

# Standard imports 
import tempfile

# Third party imports
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from pytest_postgresql import factories


# Using the factory to create a postgresql instance
socket_dir = tempfile.TemporaryDirectory()
postgresql_my_proc = factories.postgresql_proc(
    port=None, unixsocketdir=socket_dir.name)
postgresql_my = factories.postgresql('postgresql_my_proc')


@pytest.fixture(scope='function')
def setup_database(postgresql_my):

    def dbcreator():
        return postgresql_my.cursor().connection

    engine = create_engine('postgresql+psycopg2://', creator=dbcreator)
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)
    session = Session()
    yield session
    session.close()

# end setup_database()
```

##### Changes required to make pytest-postgresql work
- First we define the scope in which the fixture is going to be valid. 
  - By default, factories.postgresql_proc() will create a session-scoped fixture. We can put it in conftest.py so that it can be shared across multiple test modules. Thereafter we create a functional scoped fixture using the call to factories.postgresql(‘postgresql_my_proc’).

This plugin was not designed with SQLAlchemy in mind so we have to perform some coding gymnastics. The reason why we’d want to use SQLAlchemy is such that we can use its object models to create the tables. To do so we need to get the cursor object and pass it into the engine. This is made possible by the inner function dbcreator(), which returns a connection. Thereafter we pass it into create_engine() to create the engine using which we can automatically generate all the DDL statements to create the tables during testing.

With all the things in place, we then insert in all the required test data in yet another fixture, and run the same set of tests as per the scenario using SQLite. But this time, we can test with a higher level of confidence.
