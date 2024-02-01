---
id: l6bnit0vy4y55s69o5cv7xn
title: gunicorn
desc: ''
updated: 1705563157965
created: 1705562618090
---

https://dev.to/lsena/gunicorn-worker-types-how-to-choose-the-right-one-4n2c

The standard web servers such as Apache, and NGINX don’t know how to communicate with your Python applications. Web servers receive the request from a client(Web browser) and return a response. The web server doesn’t create the response, it only returns the response. So, a server needs to talk with a Web Application that can create a response.

And what Web Application can do? Anything your project needs it to do. I am sure you all have cool ideas to release to the world. We almost live on the web these days. So, what you build will need to communicate to the web servers in order to reach your users/audience over the internet. Therefore, we need an architecture, sort of a protocol, everyone agrees on, to bridge the request-response cycle between your web server and web application.

WSGI comes into the picture because it basically provides that bridge of your need to communicate between your Web Server and Web Application. WSGI (Web Server Gateway Interface), is a set of rules which allow a WSGI compliant server to work with a WSGI compliant Python application. WSGI also handles scaling for web servers to be able to handle thousands of requests so you don’t have to think about accepting multiple requests at a time.

### What is Gunicorn

- Gunicorn is a Python Web Server Gateway Interface (WSGI) HTTP server 
- It acts as a bridge between your web application and the web server, handling incoming HTTP requests and distributing them to your application for processing. 
- Gunicorn is known for its ability to manage concurrent connections, making it an ideal choice for deploying Python web applications that need to handle a high volume of requests.

- Within the applications code, you need to create the callable function that will be the Gunicorn entry point into the application
    ``` py
    #wsgi.py
    def app(environ, start_response):
        path = environ.get('PATH_INFO')
        if path == '/':
            response_body = "Home Page"
        else:
            response_body = "A new page"
        status = "200 OK"
        response_headers = [("Content-Length", str(len(response_body)))]
        start_response(status, response_headers)
        return [response_body]
    ```
- Later, you should start your Gunicorn workers.
    ``` sh
    gunicorn --bind 127.0.0.1:8000 --workers=4 wsgi:app
    ```
Now, you can access your web app at 127.0.0.1:8000.