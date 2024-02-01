---
id: lj84cv92l72n2uvjvs2hgyl
title: flask
desc: ""
updated: 1706259172990
created: 1700567696959
---
- [`flask-smorest` docs](https://flask-smorest.readthedocs.io/en/latest/api_reference.html#)
https://prettyprinted.com/tutorials/automatically_load_environment_variables_in_flask/

- [reCAPTCHA](https://python.plainenglish.io/how-to-implement-recaptcha-v3-with-flask-to-prevent-bot-submissions-48afa9c75d76)
- Adding Security: https://www.youtube.com/watch?v=3drtMrcfssk&ab_channel=SoumilShah

?: Using request.args.get() to grab query string arguments

#### Enhancements:
- different level of access: admin, ...
- rate-limiting
- APIFairy
- File Upload / Download
- Stream data via Web Sockets
- Tables
- webhooks
- Basic Auth
- SendGrid Email Validation
- IP Blocking
- Stop bots
- pagination

### Testing REST API: How to make a request

-   One of the most important things about any software development is to make sure that our projects work!
-   So we need to be able to test our project, run it, and make sure it does what we think it does.
-   There are two main ways of doing this:
    -   With automated tests.
    -   With manual, exploratory testing.
-   Usually you'd go with exploratory first, and then you'd make automated tests based on your manual tests.
-   For manual testing, there are two tools I use for exploratory testing: Postman and Insomnia.
-   Make sure the Flask app is running when doing tests
    ```bash
    source .venv/bin/activate
    flask run
    ```

### Working with POST requests: Creating stores

-   To create a store, we'll receive JSON from our client (in our case, Insomnia, but it could be another Python app, JavaScript, or any other language or tool).
-   Our client will send us the name of the store they want to create, and we will add it to the database!
-   For this, we will use a POST method. POST is usually used to receive data from clients and either use it, or create resources with it.
-   In order to access the JSON body of a request, we will need to import request from flask. Your import list should now look like this:

```py
# app.py
from flask import Flask, request

app = Flask(__name__)

stores = [{"name": "My Store", "items": [{"name": "my item", "price": 15.99}]}]

@app.post("/store")
def create_store():
    request_data = request.get_json()  # get JSON data from POST request
    new_store = {"name": request_data["name"], "items": []}
    stores.append(new_store)
    return new_store, 201
```

### Getting data via URL query string arguments

-   There are a few ways for clients to send us data. So far, we've seen that clients can send us JSON.
-   But data can be included in a few other places:
    -   The body (as JSON, form data, plain text, or a variety of other formats).
    -   Inside the URL, part of it can be dynamic.
    -   At the end of the URL, as query string arguments.
        -   Can use `requests.args.get(<KEY>, <DEFAULT_VALUE)` to get field values
    -   In the request headers.
-   For this request, the client will send us data in two of these at the same time: the body and the URL.
-   We can use Flask to define dynamic endpoints for our routes, and then we can grab the value that the client put inside the URL.
    -   This allows us to make URLs that make interacting with them more natural.
    -   For e.g., it's nicer to make an item by going to POST /store/My Store/item, rather than going to POST /add-item and then pass in the store name in the JSON body.
    -   To create a dynamic endpoint for our route, we do this:
        ```py
        @app.route("/store/<string:name>/item")
        ```
    -   That makes it so the route function will use a name parameter whose value will be what the client put in that part of the URL.

```py
# app.py
from flask import Flask, request

app = Flask(__name__)

stores = [{"name": "My Store", "items": [{"name": "my item", "price": 15.99}]}]

@app.post("/store/<string:name>/item")
def create_item(name):
    request_data = request.get_json()
    for store in stores:
        if store["name"] == name:
            new_item = {"name": request_data["name"], "price": request_data["price"]}
            store["items"].append(new_item)
            return new_item
    return {"message": "Store not found"}, 404
```

### Filtering using query string arguments

-   Here, using query string arguments (with a GET method), to select the items in a store:

```py
@app.get("/store/<string:name>/item")
def get_item_in_store(name):
    for store in stores:
        if store["name"] == name:
            return {"items": store["items"]}
    return {"message": "Store not found"}, 404
```


### Final Code

``` py
# app.py
from flask import Flask, request

app = Flask(__name__)

stores = [
    {
        "name": "My Store",
        "items": [
            {
                "name": "Chair",
                "price": 15.99
            }
        ]
    }
]

@app.get("/store")
def get_stores():
    return {"stores": stores}


@app.post("/store")
def create_store():
    request_data = request.get_json()
    new_store = {"name": request_data["name"], "items": []}
    stores.append(new_store)
    return new_store, 201


@app.post("/store/<string:name>/item")
def create_item(name):
    request_data = request.get_json()
    for store in stores:
        if store["name"] == name:
            new_item = {"name": request_data["name"], "price": request_data["price"]}
            store["items"].append(new_item)
            return new_item, 201
    return {"message": "Store not found"}, 404


@app.get("/store/<string:name>")
def get_store(name):
    for store in stores:
        if store["name"] == name:
            return store
    return {"message": "Store not found"}, 404


@app.get("/store/<string:name>/item")
def get_item_in_store(name):
    for store in stores:
        if store["name"] == name:
            return {"items": store["items"]}
    return {"message": "Store not found"}, 404
```

### Refactor Code and Data Model
Project Structure
``` md
API
├── db.py
├── app.py
└── dockerfile

```
1. Changes to data model
   - From now on we will be storing information about items and stores separately.
   - Will be generating uuid for new item and stores
   - Items and Stores are now separate entities
  
``` py
# db.py
stores = {}
items = {}
```

2. Changes to code
``` py
# app.py
from flask import Flask, request
from db import stores, items   # NEW: data shifted to db.py and imported over
import uuid

app = Flask(__name__)


@app.get("/store")
def get_stores():
    return {"stores": list(stores.values())}


# NEW: Assigning store_id upon creation
@app.post("/store")
def create_store():
    store_data = request.get_json()
    store_id = uuid.uuid4().hex
    store = {**store_data, "id": store_id}
    stores[store_id] = store

    return store


# NEW: Changed endpoint, The endpoint expect to receive JSON with price, name, and store_id.  
@app.post("/item")
def create_item():
    item_data = request.get_json()
    if item_data["store_id"] not in stores:
        return {"message": "Store not found"}, 404

    item_id = uuid.uuid4().hex
    item = {**item_data, "id": item_id}
    items[item_id] = item

    return item

# NEW!!!
@app.get("/item")
def get_all_items():
    return {"items": list(items.values())}


# NEW: Changed to query store by store_id
@app.get("/store/<string:store_id>")
def get_store(store_id):
    try:
        # Here you might also want to add the items in this store
        # We'll do that later on in the course
        return stores[store_id]
    except KeyError:
        return {"message": "Store not found"}, 404


# NEW: items no longer based on store
@app.get("/item/<string:item_id>")
def get_item(item_id):
    try:
        return items[item_id]
    except KeyError:
        return {"message": "Item not found"}, 404
```


---

#### docstring

``` py
"""
    Description of the endpoint.
    ---
    parameters:
      - name: parameter_name
        in: parameter_location
        type: parameter_type
        required: true|false
        description: Description of the parameter.
    responses:
      HTTP_STATUS_CODE:
        description: Response description.
    """
```