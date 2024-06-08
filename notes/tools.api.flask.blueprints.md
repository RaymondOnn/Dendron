---
id: f2xq0qvnnco8xuu4f00flm2
title: blueprints
desc: ''
updated: 1716946912970
created: 1705631202768
---

### What is a blueprint
- A Blueprint is a way to organize a group of related views and other code. 
- Blueprints allows us to break our application into modules and helps us to organize the modules in some fashion. These modules are de-coupled and also can be re-used.
- Each blueprint will have collection of views, templates and static files as similar to a Flask application object as well as configuration specific to it
- Rather than registering views and other code directly with an application, they are registered with a blueprint. 
- Then the blueprint is registered with the application when it is available in the factory function.

### Creating Blueprints

``` py
from flask import Blueprint, render_template

main = Blueprint('main', __name__, template_folder="templates")
@main.route("/")
def index():
    return render_template("main/home.html")

# code for the blueprint must be before registration    

```