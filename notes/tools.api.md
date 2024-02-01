---
id: puouu9nytpps6mx6hr4sqk6
title: API
desc: ""
updated: 1706151973381
created: 1705284113026
---
https://medium.com/@kamaleshs48/difference-between-put-post-and-patch-35ed362e05e9#:~:text=In%20summary%2C%20PUT%20is%20used,for%20partially%20updating%20existing%20resources.
- https://www.youtube.com/shorts/s33eVDbsyYM


### What is an API?

-   An API (Application Programming Interface) enables one app to communicate with another.
-   When developers of a system want to make its data and/or functionality available to external consumers — like their own mobile app, or apps that developers like you create — they’ll implement an API to enable this scenario.
-   The API is the menu of operations that can be called to access data or functionality within the corresponding system.

    ```raw
    # For e,g. API operations for managing user data:
    Add User – add a new user
    Get User – get details about an existing user
    Update User – update an existing user
    Delete User – delete a user
    List Users – get a list of all users
    ```

### what is a “REST API”?

-   REST stands for Representational State Transfer — an architectural pattern that defines certain principles for designing web services.
-   A REST API (or RESTful API as it’s sometimes called) is simply an API that adheres to that design pattern.

### How does an API work?

-   An API exists to receive requests, process data, and return responses. API requests and responses travel “over the wire” between client and server via HTTP(S).
-   The request-response cycle involves a client making a request to the server and the server sending back a response.
    1. The client sends a request to a specific URL, known as the endpoint, using a specific HTTP method.
    2. The server receives the request and extracts the necessary information, such as the route and request parameters.
    3. The server then performs the necessary logic and processes the request.
    4. The server (API) constructs a response and sends it back to the client, along with an appropriate status code.
    5. The client receives the response and can then process it accordingly.

### API Requests

```http
POST /api/v1/pokemon HTTP/1.1

Host: localhost:5000
User-Agent: curl/7.43.0
Accept: */*
Content-Type: application/json
Content-Length: 32

{"name":"Pikachu","number":25}
```

-   An HTTP request has 3 parts:
    1. **HTTP method and URL:** t
        - The HTTP methods (most common are GET, POST, PUT, DELETE) are interpreted as verbs that act on the resources defined by the URL (in this case: `/api/v1/pokemon`).
        - To handle this request we need to understand what action do for each verb and be able to map each URL to a resource in a internal data structure (e.g. a database).
        - Further information about the resource to modify can be included in a query string or in the payload of the HTTP request, but that doesn’t apply here.
    2. **HTTP Headers**:
        - headers are usually used for configuration details, authentication tokens (e.g. API keys), or client information (e.g. user agent).
        - They also contain information on the format of the payload they consume (`*/*`) and produce (`application/json`).
    3. **HTTP body**:
        - the last line of the request is the body and may contain extra data.
        - In the example show, the body carries information about the new resource to be POSTed (i.e. created).
        - In order to process the body correctly we need to check the headers for the content-type.

### Status Codes

-   Some common HTTP response status codes:
    -   1XX Information: Status codes of this format are informational and rarely encountered, They inform the client of the status of the server.
        -   100 - Continue and the server has received the request headers and is ready for the rest of the request body.
        -   102 - Processing, tells the client to wait for the server to finish.
    -   2xx Success: Codes with this format are for success messages.
        -   200 - The request was successful.
        -   201 - Created and it was successful.
        -   204 - The server processed the request and returned no content.
    -   3XX Redirection: The client is provided with a new URL to follow to get to the requested resource.
        -   301 - Moved permanently tells the client to use the new URI for all future requests.
        -   302/303 - Found at this other URI means temporarily redirected.
        -   307 - Temporary redirect
        -   308 - Permanent redirect
    -   4XX Client error: This signals client errors
        -   **400 Bad request**: the request is malformed or too large.
        -   **401 Unauthorized**: the client lacks proper credentials to access the requested resource.
        -   **403 Forbidden**: the request is refused by the server.
        -   **404 Not Found** : the resource does not exist.
        -   405 - Method not allowed is when you try to use GET on a resource that uses POST for example.
    -   5XX Server error: signals server errors.
        -   500 - Internal Server Error means something went wrong on the server.
        -   502 - Bad gateway the server received an invalid response from where it was trying to connect.
        -   503 - Service unavailable encountered when the server is overloaded or temporarily unavailable.

### Choosing a REST API development framework

-   When choosing a REST API development framework, consider these following factors:
    -   **It supports OpenAPI out of the box**:
        -   if you’re going to build a REST API, you need to use a framework that knows how OpenAPI works.
        -   Otherwise, you’ll get nasty surprises when it comes to payload validation.
        -   The easiest way to determine if a framework supports OpenAPI is by checking whether it can auto generate API documentation from your code.
        -   `Flasgger` and `flask-smorest` both do this.
    -   **Uses a robust data validation library**:
        -   validating payloads is a complex business.
        -   Your data validation library must handle optional and required properties, string formats like ISO dates and UUIDs (both dates and UUIDs are string types in OpenAPI), and strict vs loose type validation (should a string pass as an integer if it can be casted?).
        -   Also, in the case of Python, you need to make sure 1 and 0 don’t pass for True and False when it comes to boolean properties.
        -   In my experience, the best data validation libraries in the Python ecosystem are `pydantic` and `marshmallow`.
        -   From the above-mentioned libraries, `flasgger` and `flask-smorest` work with marshmallow.
    -   **It validates everything**:
        -   It validates request payloads, response payloads, URL path parameters, and URL query parameters.
        -   Some libraries only validate request payloads and provide very little support for validating URL parameters or response payloads.
        -   In those cases, make sure at least the library gives you a way to enforce your own validation rules for responses and URL parameters.
        -   If your library uses `marshmallow`, you can always use the marshmallow model directly to validate a response payload.
    -   **Maturity**:
        -   if your business (or your job) depends on the APIs you’re building, you want to build them with robust and mature libraries.
        -   Look for libraries that have been around for a while and with lots of users and contributors.
        -   Look for libraries with an active community, in which users raise issues often, but check that the issues are quickly addressed.
        -   It also helps if the library has good documentation.
