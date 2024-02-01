---
id: otomkkcxvkgsrcoz37a5c3p
title: _draft
desc: ''
updated: 1705632927712
created: 1705566562645
---
### What is Flask API
- Flask API is a web development framework built on top of the Flask microframework. 
- It provides a simple and flexible way to create RESTful APIs in Python.

- Flask API handles the communication between the client and server using HTTP requests (`GET`, `POST`, `PUT`, `DELETE`) and responds with the appropriate data in the form of JSON, XML, or HTML. 
- It also handles errors, authentication, and other necessary aspects of building an API.

### Why Flask API
- **Lightweight and modular:** Flask API is built on top of Flask, which is a lightweight and modular web framework. This makes it easy to add or remove features as needed, keeping your API simple and efficient.
- **Fast development**: Flask API comes with built-in support for common API features like authentication, URL routing, error handling, etc. This allows developers to focus on the core logic of their API, saving time and effort.
- **Flask extensions compatibility**: Flask API is compatible with all the extensions of Flask, making it even more powerful. You can easily integrate extensions like SQLAlchemy, Flask-Admin, and more to enhance your API capabilities.
- **Support for multiple formats**: Flask API provides support for multiple data formats like JSON, XML, and HTML. It also supports content negotiation, allowing clients to request a specific format for the response.
- **Secure**: Flask API provides built-in support for authentication and authorization, making it secure and reliable for handling sensitive data.
  
### Flask Fundamentals
#### Routes
- In Flask, routes are defined as the URL patterns that map a URL to a specific endpoint. 
- The routing mechanism allows the Flask application to respond to different requests made by the client.
- Flask uses the `@app.route` decorator to define routes for different endpoints. 
- Routes can be static, which respond to a specific URL, or dynamic, which accept parameters from the URL. 
    ``` py
    # An example of defining a route with a dynamic URL:
    @app.route('/user/<username>')
    def get_user(username):
        # code to retrieve user with given username from the database
        return jsonify(user) # return response in JSON format
    ```
#### Endpoints
- Endpoints are the functions or methods responsible for handling client requests. 
- Each route in Flask maps to a specific endpoint.

- To handle different HTTP methods in a Flask API, the endpoint function can utilize the `request.method` property to check the type of request and perform the appropriate logic.
    ``` py
    @app.route('/user', methods=['POST'])
    def create_user():
        # code to create a new user in the database
        return jsonify(user) # return newly created user in JSON format
    ```
    
#### Parameters and Headers
- Parameters and headers are pieces of data that are sent along with a request to the server. 
- They can contain useful information that can be used by the Flask application to process the request.
-  Access to request parameters and headers via the `request` object. 
   -  `request.args` can be used to access query parameters in a GET request
   -  `request.form` can be used for POST requests. 
   -  `request.headers` provides access to request headers sent by the client.

    ``` py
    @app.route('/user')
    def get_user():
        username = request.args.get('username') # retrieve username from query parameters
        user_agent = request.headers.get('User-Agent') # retrieve user's user agent
        # code to retrieve user with given username from the database
        return jsonify(user) # return response in JSON format
    ```

### Basic REST API Endpoint

#### Anatomy of a Flask route

```py
# app.py
from flask import Flask

stores = [{"name": "My Store", "items": [{"name": "my item", "price": 15.99}]}]

app = Flask(__name__)

@app.get("/store")
def get_stores():
    return {"stores": stores}
```
-   There are two parts to a Flask route:
    -   The endpoint decorator
        -   The endpoint decorator (`@app.get("/store")`) registers the route's endpoint with Flask. That's the `/store` bit.
        -   That way, the Flask app knows that when it receives a request for `/store`, it should run the function.
    
        > Note: `@app.get("/store")` is a shortcut for `@app.route("/store", methods=["GET"])`
    
    -   The function that should run
        -   The function's job is to do everything that it should, and at the end return something.
        -   In most REST APIs, we return JSON, but you can return anything that can be represented as text (e.g. XML, HTML, YAML, plain text, or almost anything else).
