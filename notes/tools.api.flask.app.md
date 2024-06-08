---
id: 310s4so9aswox1502c9ayu5
title: App
desc: ''
updated: 1716950051575
created: 1716946937163
---

https://hackersandslackers.com/flask-application-factory/


### The Problem
- Well-structured web apps separate logic between files and modules, typically with separation of concerns in mind. 
- Since our app depends on an "app object" that we create via `app = Flask(__name__)`, separating logic between modules means we'd be importing this app object all over the place, eventually leading to problems like circular imports. 
- The Flask Application Factory refers to a common "pattern" for solving this dilemma.

### The Application Factory
- The Application Factory is so important due to Flask's Application Context. 
- Our app's "context" takes the assortment of Python files and modules which make up our app and brings them together so that they see and work with one another. 
- For Flask to recognize the data models we have in `models.py`, our Blueprints, or anything else, Flask needs to be told that these things exist after the app is "created" with `app = Flask(__name__)`.

> Here's Flask's take on **Application factories**:
> If you are already using packages and blueprints for your application (Modular Applications with Blueprints) there are a couple of really nice ways to further improve the experience. 
> A common pattern is creating the application object when the blueprint is imported.
>
> And here's their description of the **Application context**:
> The application context keeps track of the application-level data during a request, CLI command, or other activity. Rather than passing the application around to each function, the current_app and g proxies are accessed instead.


#### Top-Down View of an Application Factory App
- When we create an app which follows the Application Factory pattern, our app should look like this:
    ``` md
    # The Flask Application Factory pattern
    
    /app
    ├── /application
    │   ├── __init__.py
    │   ├── auth.py
    │   ├── forms.py
    │   ├── models.py
    │   ├── routes.py
    │   ├── /static
    │   └── /templates
    ├── config.py
    └── wsgi.py
    ```

    - The entirety of our app lives in the `/application` folder, with the creation of our app happening inc`__init__.py`. 
    - **The init file is where we actually create what's called the Application Factory.**
    - A file called `wsgi.py` simply imports this file to serve as our app gateway. More on that another time.

#### Initiating Flask in `__init__.py`

- A properly configured Application Factory should accomplish the following:
  - Create a Flask `app` object, which derives configuration values (either from a Python class, a config file, or environment variables).
  - Initialize plugins accessible to any part of our `app`, such as a database (via `flask_sqlalchemy`), Redis (via `flask_redis`) or user authentication (via `Flask-Login`).
  - Import the logic which makes up our `app` (such as routes).
  - Register Blueprints.

    ``` py
    # __init__.py
    
    from flask import Flask
    from flask_sqlalchemy import SQLAlchemy
    from flask_redis import FlaskRedis


    # Step 1: Create global instances of Plugin Objects 
    # Setting plugins as global variables outside of init_app() makes them globally accessible to other parts of our application
    db = SQLAlchemy()
    r = FlaskRedis()


    def init_app():
        """Initialize the core application."""
    
        # Step 2: Creating Flask app object and configuration to be done via config.py:
        app = Flask(__name__, instance_relative_config=False)
        app.config.from_object('config.Config')

        # Step 3: Initialize Plugins
        # Initializing a plugin registers/bind a plugin with our Flask app so that there are available to be used
        db.init_app(app)
        r.init_app(app)

        # Step 4: Creating the app context
        # Any part of our app not imported / initialized / registered within app.app_context block effectively does not exist. 
        with app.app_context():
            # Include our Routes
            from . import routes

            # Register Blueprints
            app.register_blueprint(auth.auth_bp)
            app.register_blueprint(admin.admin_bp)

            return app

    ```



#### The Application Context
- The `app.app_context` block is the lifeblood of our Flask app 
- By importing / initializing / registering within this block, it's essentially saying "here are all the pieces of my program."
  - The first thing we do is import the base parts of our app (any Python files or logic which aren't Blueprints). 
  - Next, we register Blueprints by calling register_blueprint() on our app object and passing the Blueprint module's name, followed by the name of our Blueprint. 



#### Our App's Entry Point

- We create a file called `wsgi.py` in our app's root directory to serve as the entry point. 
- When setting up a production web server to point to your app, configure it to point to `wsgi.py`, which in turn imports and starts our entire app.

``` py
# wsgi.py
from application import init_app


app = init_app()

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```