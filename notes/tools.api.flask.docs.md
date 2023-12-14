---
id: wjo00btf0p4mvivz8x9gvjt
title: Docs
desc: ''
updated: 1700987249773
created: 1700569471034
---

https://blog.teclado.com/query-string-arguments-in-flask-rest-apis/
https://gist.github.com/jslvtr/0c3d8ea4360c02fa6c025a586ec4948f?ref=blog.teclado.com
https://rest-apis-flask.teclado.com
https://github.com/tecladocode/rest-apis-flask-python
## Improving Developer Experience

`pip install flask-smorest`

### Using `flask_smorest.abort` instead of returning errors manually
- The abort function from Flask-Smorest can help us write these messages and include a bit of extra information too.

``` diff
# app.py
 @app.get("/store/<string:store_id>")
 def get_store(store_id):
     try:
         # Here you might also want to add the items in this store
         # We'll do that later on in the course
         return stores[store_id]
     except KeyError:
-        return {"message": "Store not found"}, 404
+        abort(404, message="Store not found.")

```

### Validating Inputs
- At the moment when we create items and stores, we expect there to be certain items in the JSON body of the request.
- If those items are missing, the app will return an error 500, which means "Internal Server Error".
- Instead of that, it's good practice to return an error 400 and a message telling the client what went wrong.
- To do so, let's inspect the body of the request and see if it contains the data we need.

``` diff
# app.py
@app.post("/store")
def create_store():
    store_data = request.get_json()
+    if "name" not in store_data:
+        abort(
+            400,
+            message="Bad request. Ensure 'name' is included in the JSON payload.",
+        )
+    for store in stores.values():
+        if store_data["name"] == store["name"]:
+            abort(400, message=f"Store already exists.")

    store_id = uuid.uuid4().hex
    store = {**store_data, "id": store_id}
    stores[store_id] = store

    return store

```
### Adding New Endpoints

- We want to add some endpoints for added functionality:
  - DELETE /item/<string:item_id> so we can delete items from the database.
  - PUT /item/<string:item_id> so we can update items.
  - DELETE /store/<string:store_id> so we can delete stores.
#### Deleting items
This is almost identical to getting items, but we use the del keyword to remove the entry from the dictionary.
``` py
app.py
@app.delete("/item/<string:item_id>")
def delete_item(item_id):
    try:
        del items[item_id]
        return {"message": "Item deleted."}
    except KeyError:
        abort(404, message="Item not found.")
```

#### Updating items
This is almost identical to creating items, but in this API we've decided to not let item updates change the store_id of the item. So clients can change item name and price, but not the store that the item belongs to.

This is an API design decision, and you could very well allow clients to update the store_id if you want!
``` py
app.py
@app.put("/item/<string:item_id>")
def update_item(item_id):
    item_data = request.get_json()
    # There's  more validation to do here!
    # Like making sure price is a number, and also both items are optional
    # You should also prevent keys that aren't 'price' or 'name' to be passed
    # Difficult to do with an if statement...
    if "price" not in item_data or "name" not in item_data:
        abort(
            400,
            message="Bad request. Ensure 'price', and 'name' are included in the JSON payload.",
        )
    try:
        item = items[item_id]
        item |= item_data

        return item
    except KeyError:
        abort(404, message="Item not found.")
```
#### Deleting stores
This is very similar to deleting items!
``` py
app.py
@app.delete("/store/<string:store_id>")
def delete_store(store_id):
    try:
        del stores[store_id]
        return {"message": "Store deleted."}
    except KeyError:
        abort(404, message="Store not found.")
```

### Improving code structure

1. Creating a blueprint for each related group of resources
   - The Blueprint arguments are the same as the Flask Blueprint1, with an added optional description keyword argument:
     - `stores` is the name of the blueprint. This will be shown in the documentation and is prepended to the endpoint names when you use url_for (we won't use it).
     - `__name__` is the "import name".
     - The description will be shown in the documentation UI.

    ``` py
    resources/store.py
    import uuid
    from flask import request
    from flask.views import MethodView
    from flask_smorest import Blueprint, abort
    from db import stores


    blp = Blueprint("stores", __name__, description="Operations on stores")
    ```

2.  Adding our MethodViews. 
    - These are classes where each method maps to one endpoint. The interesting thing is that method names are important
    - The endpoint is associated to the MethodView class. Here, the class is called Store and the endpoint is `/store/<string:store_id>`.
    - There are two methods inside the Store class: get and delete. These are going to map directly to `GET /store/<string:store_id>` and `DELETE /store/<string:store_id>`.

    ``` py
    # resources/store.py
    @blp.route("/store/<string:store_id>")
    class Store(MethodView):
        def get(self, store_id):
            try:
                return stores[store_id]
            except KeyError:
                abort(404, message="Store not found.")

        def delete(self, store_id):
            try:
                del stores[store_id]
                return {"message": "Store deleted."}
            except KeyError:
                abort(404, message="Store not found.")
    ```

    Now, still inside the same file, we can add another MethodView with a different endpoint, for the /store route:
    ``` py
    # resources/store.py
    @blp.route("/store")
    class StoreList(MethodView):
        def get(self):
            return {"stores": list(stores.values())}

        def post(self):
            store_data = request.get_json()
            if "name" not in store_data:
                abort(
                    400,
                    message="Bad request. Ensure 'name' is included in the JSON payload.",
                )
            for store in stores.values():
                if store_data["name"] == store["name"]:
                    abort(400, message=f"Store already exists.")

            store_id = uuid.uuid4().hex
            store = {**store_data, "id": store_id}
            stores[store_id] = store

            return store
    ```

Let's do the same thing with the resources/item.py file
``` py
# resources/item.py
import uuid
from flask import request
from flask.views import MethodView
from flask_smorest import Blueprint, abort

from db import items

blp = Blueprint("Items", __name__, description="Operations on items")


@blp.route("/item/<string:item_id>")
class Item(MethodView):
    def get(self, item_id):
        try:
            return items[item_id]
        except KeyError:
            abort(404, message="Item not found.")

    def delete(self, item_id):
        try:
            del items[item_id]
            return {"message": "Item deleted."}
        except KeyError:
            abort(404, message="Item not found.")

    def put(self, item_id):
        item_data = request.get_json()
        # There's  more validation to do here!
        # Like making sure price is a number, and also both items are optional
        # Difficult to do with an if statement...
        if "price" not in item_data or "name" not in item_data:
            abort(
                400,
                message="Bad request. Ensure 'price', and 'name' are included in the JSON payload.",
            )
        try:
            item = items[item_id]

            # https://blog.teclado.com/python-dictionary-merge-update-operators/
            item |= item_data

            return item
        except KeyError:
            abort(404, message="Item not found.")


@blp.route("/item")
class ItemList(MethodView):
    def get(self):
        return {"items": list(items.values())}

    def post(self):
        item_data = request.get_json()
        # Here not only we need to validate data exists,
        # But also what type of data. Price should be a float,
        # for example.
        if (
            "price" not in item_data
            or "store_id" not in item_data
            or "name" not in item_data
        ):
            abort(
                400,
                message="Bad request. Ensure 'price', 'store_id', and 'name' are included in the JSON payload.",
            )
        for item in items.values():
            if (
                item_data["name"] == item["name"]
                and item_data["store_id"] == item["store_id"]
            ):
                abort(400, message=f"Item already exists.")

        item_id = uuid.uuid4().hex
        item = {**item_data, "id": item_id}
        items[item_id] = item

        return item
```

3. Import blueprints and Flask-Smorest configuration
Finally, we have to import the Blueprints inside app.py, and register them with Flask-Smorest:
``` py
app.py
from flask import Flask
from flask_smorest import Api

from resources.item import blp as ItemBlueprint
from resources.store import blp as StoreBlueprint


app = Flask(__name__)

app.config["PROPAGATE_EXCEPTIONS"] = True
app.config["API_TITLE"] = "Stores REST API"
app.config["API_VERSION"] = "v1"
app.config["OPENAPI_VERSION"] = "3.0.3"
app.config["OPENAPI_URL_PREFIX"] = "/"
app.config["OPENAPI_SWAGGER_UI_PATH"] = "/swagger-ui"
app.config["OPENAPI_SWAGGER_UI_URL"] = "https://cdn.jsdelivr.net/npm/swagger-ui-dist/"

api = Api(app)

api.register_blueprint(ItemBlueprint)
api.register_blueprint(StoreBlueprint)
```

I've also added a few config variables to the app.config. The PROPAGATE_EXCEPTIONS value is used so that when an exception is raised in an extension, it is bubbled up to the main Flask app so you'd see it more easily.

The other config values are there for the documentation of our API, and they define things such as the API name and version, as well as information for the Swagger UI.

Now you should be able to go to http://127.0.0.1:5000/swagger-ui and see your Swagger documentation rendered out!

### Adding Data Validation via `marshmallow`
####Writing the ItemSchema
Here's the definition of an Item using marshmallow (this is called a schema):
``` py
# schemas.py
from marshmallow import Schema, fields


class ItemSchema(Schema):
    id = fields.Str(dump_only=True) # dump_only implies field only available in output
    name = fields.Str(required=True)
    price = fields.Float(required=True)
    store_id = fields.Str(required=True)
```
#### Writing the ItemUpdateSchema

- When we want to update an Item, we have different requirements than when we want to create an item.
- The main difference is that the incoming data to our API when we update an item is different than when we create one. Fields are optional, such that not all item fields should be required. Also, you may not want to allow certain fields at all.
- This is the ItemUpdateSchema:
    ``` py
    # schemas.py
    class ItemUpdateSchema(Schema):
        name = fields.Str()
        price = fields.Float()
    ```

As you can see, these are not required=True. I've also taken off the id and store_id fields, because:

This schema will only be used for incoming data, and we will never receive an id.
We don't want clients to be able to change the store_id of an item. If you wanted to allow this, you can add the store_id field here as well.
#### Writing the StoreSchema
``` py
# schemas.py
class StoreSchema(Schema):
    id = fields.Str(dump_only=True)
    name = fields.Str(required=True)
```

There's not much to explain here! Similar to the ItemSchema, we have id and name since those are the only fields we need for a store.