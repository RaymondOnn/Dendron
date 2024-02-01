---
id: 1mxfq6cn9pd4xf8ffkeyqk5
title: flask
desc: ''
updated: 1706249931348
created: 1692165593799
---

- Theory: https://www.youtube.com/watch?v=qbLc5a9jdXo&ab_channel=CalebCurry
- 
- https://www.freecodecamp.org/news/structuring-a-flask-restplus-web-service-for-production-builds-c2ec676de563/
- https://towardsdatascience.com/flask-in-production-minimal-web-apis-2e0859736df
- *** https://blog.miguelgrinberg.com/post/designing-a-restful-api-using-flask-restful
- https://auth0.com/blog/sqlalchemy-orm-tutorial-for-python-developers/#SQLAlchemy-Introduction

https://www.youtube.com/@prettyprinted
https://github.com/miguelgrinberg


### What is Flask
- Flask is a framework for building web servers in Python. 
- A web server is software that responds to HTTP requests with the appropriate data and files.
- Unlike other frameworks, it does not force the developer to conform to a particular directory structure.
- it is unopinionated, allowing you to use whichever tools you prefer to perform actions such as connecting to a database and validating form inputs.
- To install flask: `pip install flask`



### Serving Some HTML
``` py
# main.py
from flask import Flask

# __name__ stores the name of current module
# Flask needs it to know where to find files
app = Flask(__name__)  

@app.route("/")   # <--- converts a regular function into a Flask view function, which converts the return value into a HTML response
def index():
    return '<h1>Hello from Flask!</h1>'
```
- Using `@app.route` 
  - we define URL paths like “/”, “/home”, “/about” 
  - bind respective functions to invoke.
  - converts the binded function into a Flask view function, which converts the return value into a HTML response
- Flask app maintains the registry of all the paths and their respective functions to invoke. 
  - When a specific URL path is entered on browser say “/”, it checks if the “/” is registered in the registry of Flask application object (app) and then invokes it’s respective function. 
  - If URL path “/” is not registered , then 404 Not found error will appear.
- To run the application, we use the command: `flask --app main run`
  - `--app`: Specifies where the app is. Default: app.py
  - `--reload`: Indicates to flask to auto refresh. Great for development work. 
- By default, the server will start running on http://localhost:5000

### Serving an HTML file

``` html
# template/index.html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Flask Website</title>
  </head>
  <body>
	<h1>Hello from Flask!</h1>
  </body>
</html>
```
``` py
# main.py
from flask import Flask, render_template

app = Flask(__name__)  

@app.route("/")   
def index():
    return render_template('index.html') 
# render_template allows us to serve html templates
```

### Creating an API Route
- Flask can also be used to serve data through an API route. 
    ``` py
    # main.py
    @app.route('/data')
    def data():
        return { "status": "OK", "msg": "Hello from Flask API" }
    ```
- If a function returns a dict or list, the value is converted to JSON and sent as a response. 
- If you restart the server and open http://localhost:5000/data, you should see the following:
    ``` json
    { "status": "OK", "msg": "Hello from Flask API" }
    ```

---
