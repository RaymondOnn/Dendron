---
id: 3odtm4ogjg4fzek06izdsf5
title: httpx
desc: ''
updated: 1705563778210
created: 1693796294809
---

## Basic Usage: Comparing with requests

```py
# requests
import requests

r = requests.get("https://api.github.com/user", auth=('user', 'pass'))

print(r.status.code)
print(r.content)
print(r.headers['content-type'])
print(r.encoding)
print(r.json())

# httpx
import httpx

r = httpx.get("https://api.github.com/user", auth=('user', 'pass'))

print(r.status.code)
print(r.content)
print(r.headers['content-type'])
print(r.encoding)
print(r.json())
```

### Same functionality as `requests`

``` py
import httpx

# CRUD operations
r = httpx.get('https://httpbin.org/get')
r = httpx.post('https://httpbin.org/post', data={'key':'value'})
r = httpx.put('https://httpbin.org/put', data={'key':'value'})
r = httpx.delete('https://httpbin.org/delete')
r = httpx.head('https://httpbin.org/get')
r = httpx.options('https://httpbin.org/get')

# Pass parameters
params = {'key1': 'value1', 'key2': 'value2'}
r = httpx.get('https://httpbin.org/get', params=params)

# Custom headers
headers = {'user-agent': 'my-app/0.0.1'}
r = httpx.get('https://httpbin.org/get', headers=headers)

# Send JSON encoded data
data = {'integer': 123, 'boolean': True, 'list': ['a', 'b', 'c']}
r = httpx.post('https://httpbin.org/post', json=data)

# Cookies
r = httpx.get('https://httpbin.org/cookies/set?chocolate=chip')
print(r.cookies['chocolate'])

# Authentication
httpx.get("https://example.com", auth=("my_user", "password123"))

# Timeout: Default = 3s
httpx.get('https://github.com/', timeout=1)
```

## Advanced Usage: Using the Client object

- Without using the Client object, HTTPX has to establish a new connection for every single request (connections are not reused).
- The Client instance uses HTTP connection pooling. 
- This means that when you make several requests to the same host, the Client will reuse the underlying TCP connection, instead of recreating one for every single request.


```py
import httpx

with httpx.Client() as client:
    r = client.get('https:..example.com)

# Client methods accept the same arguments as httpx.get(), httpx.post() etc
with httpx.Client() as client:
    headers = {'X-Custom': 'value'}
    r = client.get('https://httpbin.org/get'), headers=headers

print(r.status_code, r.json())


# Sharing configurations across requests
url = 'https://https://httpbin.org/headers'
headers = {'User-Agent': 'my-app/0.0.1'}

with httpx.Client(headers=headers) as client:
    r = client.get(url)
    print(r.json()['headers']['User-Agent'])

```

## Async Usage
https://www.twilio.com/blog/asynchronous-http-requests-in-python-with-httpx-and-asyncio
``` py
import asyncio
import httpx

async def main():
    async with httpx.AsyncCleint() as client:
        response = await client.get('https://www.example.com')
        print(response)

asyncio.run(main())
```

### Best Practices for Async

```py
import asyncio
import httpx
import time

# ===================== Wrong Way ==================
async def main():
    async with httpx.AsyncClient() as client:
        pokemons = []
        for number in range(1, 151):
            pokemon_url = f'https://pokeapi.co/api/v2/pokemon/{number}'
            resp = await client.get(pokemon_url)
            pokemons.append(resp.json()['name'])

asyncio.run(main())

# ===================== Better Way ==================
async def get_pokemon(client, url):
    resp = await client.get(url)
    return resp.json()['name']

    
async def main():
    async with httpx.AsyncClient() as client:
        tasks = []
        for number in range(1, 151):
            pokemon_url = f'https://pokeapi.co/api/v2/pokemon/{number}'
            tasks.append(asyncio.create_task(get_pokemon(client, pokemon_url)))

        orig_pokemon - await asyncio.gather(*tasks)

asyncio.run(main())
```

## Web App Testing

- Can configure an httpx client to call directly into a Python web app using the WSGI protocol

**Flask Example**

```py
# Using httpx as a client inside test cases
from flask import Flask
import httpx

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World"

with httpx.Client(app=app, base_url="http://testserver:) as client:
    r = client.get("/")
    print(r.text)
    assert r.status_code == 200
    assert r.text == "Hello World"
```

**FastApi Example**

```py
# https://fastapi.tiangolo.com/advanced/async-tests/
from fastapi import FastAPI
import httpx
import pytest

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Tomato"}

import pytest
from httpx import AsyncClient

@pytest.mark.anyio
async def test_root():
    async with httpx.AsynClient(app=app, base_url="http://test") as ac:
        response = await ac.get("/")
    
    assert response.status_code == 200
    assert response.json() == {"message":"Tomato"}
```
