---
id: 322be92zsmwea33klsh0h4x
title: marshmallow
desc: ""
updated: 1705885661993
created: 1705627715072
---

## Marshmallow

### What is Marshmallow

-   Marshmallow is a Python library used for object serialization and deserialization.
-   It provides a simple and convenient way to convert complex data types (i.e. objects or data structures), into JSON, XML, or other formats suitable for storage or communication with other systems.
-   Also allows deserializing data back into Python objects.

### Getting Started

```py
from marshmallow import Schema, fields, post_load

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __repr__(self):
        return f'{ self.name } is { self.age } years old.'

# Marshmarllow schema define to map the simple data structure to the complex python object
class PersonSchema(Schema):
    # define the simple data structures
    name = fields.String()
    age = fields.Integer()

    @post_load  # define what desserialization will do..
    def create_person(self, data, **kwargs):
        return Person(**data)

input_data = {}

input_data['name'] = input('What is your name? ')
input_data['age'] = input('What is your age? ')
```

-   Creating an instance of Person the normal way
    ```py
    # Init Person object the normal way
    person = Person(name=input_data['name'], age=input_data['age'])
    print(person)
    >>> Anthony is 30 years old
    ```
-   Creating a Person object via deserializing (i.e. data structures to python objects)
    ```py
    schema = PersonSchema()
    #.load() implies deserialization
    person_1 = schema.load(input_data)
    print(person_1)
    >>> Anthony is 30 years old
    ```
-   Serializing the `person_1` object back to simple data structures
    ```py
    # dump implies serialization
    result = schema.dump(person)
    print(result)
    > {'name': Anthony, 'age': 30}
    ```

### Working with `marshmallow`

1. **Schema Definition**

    -  Define schemas, i.e. specify the fields of an object, their types, and various validation rules.
        -  This tells `marshmallow` the simple data structures to expect
        -  It's also where mapping of the two sides happen (i.e. simple data structure on one side, complex python objects on the other) so that conversion can happen

    <br>

    ```py
    from marshmallow import Schema, fields

    class BookSchema(Schema):
        title = fields.Str()
        author = fields.Str()
        year_published = fields.Int()
    ```

2. **Serialization**

   -   transform data into a serialized format (i.e. JSON or XML) and back.

    <br>

    ```py
    # Serializing a Book object to JSON:

    book = Book(title="Sample Book", author="John Doe", year_published=2022)
    book_schema = BookSchema()
    serialized_book = book_schema.dump(book)
    print(serialized_book)
    # Output:
    >>> {'author': 'John Doe', 'title': 'Sample Book', 'year_published': 2022}

    # Deserializing JSON data into a Book object:
    json_data = '{"title": "Another Book", "author": "Jane Smith", "year_published": 2021}'
    book_schema = BookSchema()
    deserialized_book = book_schema.load(json_data)
    print(deserialized_book)

    # Output:
    >>> {'author': 'Jane Smith', 'title': 'Another Book', 'year_published': 2021}
    ```

[**Nesting Schemas**](https://marshmallow.readthedocs.io/en/stable/nesting.html)

-   Schemas can be nested to represent relationships between objects (e.g. foreign key relationships). For example, a Blog may have an author represented by a User object.

    ```py
    from marshmallow import Schema, fields


    class UserSchema(Schema):
        name = fields.String()
        email = fields.Email()
        created_at = fields.DateTime()


    class BlogSchema(Schema):
        title = fields.String()
        author = fields.Nested(UserSchema)  # each author is a user
    ```

> **Note**: If the field is a collection of nested objects, pass the Nested field to List.
>
> ```py
> collaborators = fields.List(fields.Nested(UserSchema))
> ```

### Validation

-   Given that serialization is finicky (i.e. data needs to be as specified for serialization / deserialization to work), a side effect is that `marshmallow` can be used for data validation
-   This has many benefits i.e. maintain data integrity, improves data quality, and facilitates interoperability between different components or systems that exchange data
-   3 options for validation:

    -   `validate`: Use a pre-built validator directly on the field in the schema definition
        -   It covers common use cases like checking length or RegEx matches for strings or range for integers.
        -   The full list of choices is available [here](https://marshmallow.readthedocs.io/en/stable/marshmallow.validate.html).

    ```py
    from marshmallow import fields, validate

    ACCEPTED_GENRES = ["Poetry", "Fantasy", "Historical Fiction", "Memoir",
                    "Literary Fiction", "Horror", "Drama", "Children's"]

    class BookSchema(ma.SQLAlchemySchema):
        # Meta sub-class setup

        # check the length of the title string
        # return the error message if length not met
        title = fields.String(validate=validate.Length(min=1, max=150), \
                        error="Title must be less than 150 characters")
        # check that the value for genre appears in the list of accepted genres
        genre = fields.String(validate=validate.OneOf(ACCEPTED_GENRES), \
                                error="Genre not valid")
        # check that the integer falls within an accepted range
        # (no billion page books!)
        page_count = fields.Integer(strict=True, \
                        validate=validate.Range(min=1, max=25000), \
                        error="Page count must be between 1 and 25000")

    ```

    -   `validates`: Create a custom function for a specific field in the schema

        -   Great for comparing an incoming value for a single field to existing rows in your database

        ```py
        from marshmallow import validates, ValidationError

        class UserSchema(ma.SQLAlchemySchema):
            # Meta sub-class setup

            # add validates decorator and specify the relevant field name
            @validates("username")
            # create an instance method that takes a value for username
            def validates_username(self, username):
                # check if a user already exists with that username
                if User.query.filter(User.username == username).first():
                        raise ValidationError("That username is taken")
        ```

    -   `validates_schema`: Create a custom function for a specific instance of the schema’s model (the object itself)

        ```py
        from marshmallow import validates_schema, ValidationError

        class ReviewSchema(ma.SQLALchemySchema):
        # Meta sub-class setup

        # any field specifications
        # add validates_schema decorator
        # no field needed, since it's looking at the whole object
        @validates_schema
        def validate_object(self, data, **kwargs):
            # check the database to see if there's already a review
            # with the same book and user id
            if review := Review.query.filter(Review.book_id == data["book_id"]) \
                                .filter(Review.user_id == data["user_id"]) \
                                .first():
                    # raise an error
                    # that this book has already been reviewed by this user
                    raise ValidationError("Book already reviewed")
        ```

-   When validating data, the appropriate schema to used to ensure that the data is in the correct format and contains the required fields.

    ```py
    from flask import request, jsonify
    from myapp.models import Book
    from myapp.schemas import BookPostSchema

    @app.route('/books', methods=['POST'])
    def create_book():
        data = request.get_json()
        schema = BookPostSchema()
        errors = schema.validate(data) # <---- Data Validation here
        if errors:
            return jsonify({'errors': errors}), 400  # Bad Request
        book = Book(**data)
        db.session.add(book)
        db.session.commit()
        return jsonify(schema.dump(book)), 201  # Created

    if __name__ == '__main__':
        app.run()
    ```

### Common errors

-   Here’s some common errors and a starting point of where to look.
    -   **`ImportError`**:
        -   This may surface as `ImportError: cannot import name 'NameOfSchema' from partially initialized module (likely due to circular import)`.
        -   This can occur if you have mirrored nested fields (e.g. books on reviews and reviews on books) and are importing each schema into each file.
        -   You can resolve this by typing one of the schemas as a string, e.g. "ReviewSchema" for the nested reviews field on books, to break the circular import.
    -   **`AttributeError`**:
        -   This may surface as `AttributeError: 'NameOfSchema' has no attribute 'id'`
        -   Check that you have appropriately defined many=True for any many-to-many or one-to-many relationships and that this schema isn’t trying to interpret many as one .
    -   **`ValueError`**:
        -   This may surface as `ValueError: Invalid fields for 'NameOfSchema'` .
        -   Check your nested fields and that you are not trying to serialize a field that’s not exposed on the original schema.
        -   For example, if UserSchema wants to display the nested books field with a “description,” but the BookSchema itself does not include a “description” field.
