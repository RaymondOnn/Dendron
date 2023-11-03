---
id: llobx70d0zhd9bsaxhj4ecp
title: domain_driven_design
desc: ''
updated: 1697308369702
created: 1696696197865
---
<https://www.youtube.com/watch?v=v0wBmQq8qcA>
<https://medium.com/profitoptics/domain-driven-design-translating-it-to-data-products-in-real-life-bf6bdd4115c>

- Value Objects
- Entities
- Repositories
- Domain Events
- Factories
- Services
- Layered Architecture
-

### What is Domain Driven Design

- Domain-Driven Design (DDD) is a methodology that aims to align software development with the complex realities of a business domain.
- At the heart of DDD are domain objects and aggregates, powerful design patterns that play a pivotal role in creating robust and maintainable software.

### Domain Objects

- In DDD, a domain object represents a concept or entity from the real-world business domain that the software is modeling.
- These objects encapsulate both data and behavior related to the domain, making them self-contained and coherent.
- Domain objects are characterized by the following key attributes:

```py
class Product:
    def __init__(self, sku, name, price):
        self.sku = sku  # Unique identifier for the product
        self.name = name  # Name of the product
        self price = price  # Price of the product

    def __str__(self):
        return f"{self.name} (SKU: {self.sku}) - Price: ${self.price:.2f}"
```

### Aggregates

- Aggregates define boundaries and transactional consistency rules within the domain.
- An aggregate is **a cluster of domain objects that are treated as a single unit**, ensuring that all changes to the aggregate’s state are consistent.
- Aggregates help maintain data integrity and encapsulate complex business rules.

```py
class Order:
    def __init__(self, order_number, customer):
        self.order_number = order_number  # Unique order identifier
        self.customer = customer  # Customer placing the order
        self.line_items = []  # List of line items in the order

    def add_line_item(self, product, quantity):
        # Create a line item for the order
        line_item = LineItem(product, quantity)
        self.line_items.append(line_item)

    def calculate_total(self):
        # Calculate the total order amount based on line items
        total = sum(item.calculate_subtotal() for item in self.line_items)
        return total

class LineItem:
    def __init__(self, product, quantity):
        self.product = product  # Product in the line item
        self.quantity = quantity  # Quantity of the product in the order

    def calculate_subtotal(self):
        # Calculate the subtotal for the line item
        subtotal = self.product.price * self.quantity
        return subtotal
```

#### Interpretation

- `Order` Class:
  - The `Order` class represents an aggregate.
  - It has attributes such as
    - `order_number` (a unique order identifier)
    - `customer` (the customer placing the order)
    - `line_items` (a list of line items in the order).
  - The `add_line_item` method allows adding line items to the order. It creates a LineItem object and appends it to the line_items list.
  - The `calculate_total` method calculates the total order amount based on the line items by summing the subtotals of each line item.
- `LineItem` Class:
  - The `LineItem` class represents another domain object within the aggregate.
  - It has attributes such as
    - `product` (the product in the line item)
    - `quantity` (the quantity of the product in the order).
  - The `calculate_subtotal` method calculates the subtotal for the line item by multiplying the product's price by the quantity.

This code models an order as an aggregate, where an order consists of multiple line items.

- The Order class serves as the root of the aggregate, and it encapsulates the business logic for managing line items and calculating the total order amount.
- The LineItem class represents an entity within the aggregate, responsible for managing the details of individual products and quantities within an order.

### Benefits of Domain Objects and Aggregates in DDD

- Maintainability: DDD patterns make it easier to maintain and evolve software systems over time. Changes to the business domain can be localized within aggregates, minimizing the impact on the rest of the system.
- Scalability: Aggregates provide natural boundaries, allowing for parallel development and scalability. Different teams can work on different aggregates without stepping on each other’s toes.
- Domain Focus: DDD encourages a deep understanding of the business domain, resulting in software that closely aligns with real-world requirements.
- Consistency and Integrity: Aggregates ensure data consistency, preventing invalid or inconsistent states within the domain.

### Line Items

- A line item is a fundamental concept in business and e-commerce domains. It represents an individual item or product within an order, invoice, or receipt.
- Line items capture detailed information about each item contributing to the total. They typically include attributes such as the item’s name, quantity, price, and relevant discounts or taxes.
- Line items are essential because they provide granularity and transparency in financial transactions and order processing. They allow for accurate calculation of totals, tracking of individual items, and resolution of discrepancies or disputes.
- In DDD, modelling line items involves defining them as domain entities or value objects, depending on the context

#### Line Item as an Entity

- If line items **need to be individually identified and tracked throughout their lifecycle**, you can model them as entities.
- Each line item will have a unique identifier, making it possible to reference, update, and delete them individually.

``` py
# In this example, LineItem is an entity with attributes like product, quantity, and price. 
# You can perform operations on each line item individually.
class LineItem:
    def __init__(self, product, quantity, price):
        self.product = product
        self.quantity = quantity
        self.price = price
```

#### Line Item as a Value Object

- If line items **don’t need to be individually identified** and can be treated as part of a larger whole (e.g., a collection of line items within an order), you can model them as value objects.
- Value objects have no distinct identity and are immutable.

``` py
# In this case, the LineItem is a value object that captures the properties of an individual line item but doesn't have its own identity.
class LineItem:
    def __init__(self, product, quantity, price):
        self.product = product
        self.quantity = quantity
        self.price = price
```

#### Aggregate Root

- Line items are often part of a larger aggregate, such as an order or invoice.
- In DDD, the aggregate root manages its constituent line items.
- Line items are typically accessed and manipulated through the aggregate root.

```py
class Order:
    def __init__(self, customer):
        self.customer = customer
        self.line_items = []

    def add_line_item(self, product, quantity, price):
        line_item = LineItem(product, quantity, price)
        self.line_items.append(line_item)

    # Other order-related methods...
```

### The unit of work pattern?

- In this pattern, a unit of work represents a single transactional operation that involves a set of related operations that should be treated as a single unit of work, either all succeeding or all failing.
- The pattern helps ensure that data changes are consistently and reliably persisted to a database, and that any errors or exceptions are handled appropriately.
- The unit of work pattern typically involves the use of a transaction manager or a similar component to manage the transactional behavior, and may also include the use of a repository to manage data access and storage.

#### Why unit of work pattern?

- Transactional consistency: By using a UoW, we can ensure that all database operations within a single unit of work are treated as a single transaction. If any operation fails, the entire transaction is rolled back, ensuring that the database remains in a consistent state.
- Simplified code: The UoW pattern can help simplify code and make the code easier to read and maintain by encapsulating the complexity of managing database transactions within a single class.
- Improved performance: The UoW pattern can also help improve performance by reducing the number of database connections and transactions required through grouping related operations within a single unit of work,
- Code reusability:The UoW pattern can be used to encapsulate the transactional behavior of multiple types of operations across an application, improving code reusability.

#### Code Example

Here’s an example of how you might use the unit of work pattern in a Python application:

``` py
import sqlalchemy
from sqlalchemy.orm import sessionmaker
from contextlib import contextmanager

# Set up the database connection and session factory
engine = sqlalchemy.create_engine('sqlite:///example.db')
Session = sessionmaker(bind=engine)

# Define the unit of work class
class UnitOfWork:
    def __init__(self):
        self.session = None
    
    def __enter__(self):
        self.session = Session()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_val:
            self.session.rollback()
        else:
            self.session.commit()
        self.session.close()

    @contextmanager
    def get_session(self):
        """Helper function to manage sessions"""
        session = Session()
        try:
            yield session
        except:
            session.rollback()
            raise
        finally:
            session.close()

# Example usage
with UnitOfWork() as uow:
    with uow.get_session() as session:
        # Perform database operations within the session context
        # e.g. create, read, update, delete (CRUD) operations
        # If any exceptions occur, the transaction will be rolled back
        user1 = User(name="John Doe", email="john@example.com")
        session.add(user1)
        session.commit()
```
