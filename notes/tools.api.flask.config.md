---
id: dcgzy2jshzo2jxwm2957q4b
title: config
desc: ""
updated: 1706251263989
created: 1705824559417
---

### `.flaskenv` vs `.env`

-   `.flaskenv`
    -   Meant for Flask's own configuration variables
    -   Hence, can be added to your source control, as it does not contain any secrets or passwords.
    -   The automatic import via the flask command suffices here since its contents are only needed when running the application through the flask command.
    -   Flask's FLASK_APP and FLASK_DEBUG can only be supplied via `.flaskenv` because these are needed very early in the application bootstrap process, before the app instance and its config object exist.
-   `.env`

    -   Meant from application configuration variables
    -   The .env file is not supposed to be added to source control to ensure that your secrets are protected.
    -   Recommended to explicitly import the contents of the .env file.

    ```py
    # config.py: Import a .env file with environment variables.

    import os
    from dotenv import load_dotenv

    basedir = os.path.abspath(os.path.dirname(__file__))
    load_dotenv(os.path.join(basedir, '.env'))

    class Config(object):
        # ...
    ```

### `config.from.prefixed_env`

1. Create `.env` file.

    - Note: for this to work, the name of the environment variables **must** start with `FLASK_`

    ```raw
    # .env

    FLASK_SQLALCHEMY_DATABASE_URI=sqlite:///db.sqlite3
    FLASK_SECRET_KEY=some_value
    ```

2. load the environment variables via `.config.from.prefixed_env()`

    ```py
    from flask import Flask
    import os

    def create_app():
        app = Flask(__name__)
        app.config.from_prefixed_env()  # <---- read from
        print(app.config['SECRET_KEY'])
        return app

    ```

### API Configuration

```py
class APIConfig:
    API_TITLE = <API_NAME>
    API_VERSION = v1
    OPENAPI_VERSION = "3.0.3"
    OPENAPI_URL_PREFIX = "/"
    OPENAPI_SWAGGER_UI_PATH = "/swagger-ui"
    OPENAPI_SWAGGER_UI_URL = "https://cdn.jsdelivr.net/npm/swagger-ui-dist/"

    OPENAPI_REDOC_PATH = '/redoc'
    OPENAPI_REDOC_UI_URL = "https://cdn.jsdelivr.net/npm/redoc@next/bundles/redoc.standalone.js"

app.config.from_object(APIConfig)
```
