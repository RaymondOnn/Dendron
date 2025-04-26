---
id: ffa5ye0lbpbkl5ofccjx6g1
title: api
desc: ''
updated: 1732427773236
created: 1732424222784
---

### Data Ingestion via API
<https://python.plainenglish.io/python-api-best-practices-10-expert-tips-for-robust-integrations-43430fa9734e>

#### Making Reliable HTTP Requests with Comprehensive Error Handling

- Before:
  - Beginners typically make API requests without checking for response success or handling different error scenarios.
  - This code assumes all responses are successful and in JSON format, which can break the application if the API returns errors or invalid data.

``` py
import requests

response = requests.get('https://jsonplaceholder.typicode.com/posts')
data = response.json()
print(data)
```

- After:
  - Handle errors explicitly, managing different failure scenarios (HTTP errors, timeouts, and network issues) and ensuring response consistency.
  - Makes the code resilient to API errors and unexpected content types, ensuring it handles API inconsistencies gracefully.

``` py
import requests

def fetch_data(url, timeout=10):
    try:
        response = requests.get(url, timeout=timeout)
        response.raise_for_status()  # Raises HTTPError for bad responses
        if response.headers.get('Content-Type') == 'application/json; charset=utf-8':
            return response.json()
        else:
            raise ValueError("Unexpected Content-Type in response")
    except requests.exceptions.HTTPError as http_err:
        print(f"HTTP error occurred: {http_err}")
    except requests.exceptions.Timeout:
        print("Request timed out.")
    except requests.exceptions.RequestException as err:
        print(f"An error occurred: {err}")
    except ValueError as e:
        print(e)
    return None

data = fetch_data('https://jsonplaceholder.typicode.com/posts')
```

#### Efficient Rate Limiting with Automatic Backoff

- Before:
  - Beginners often hit rate limits by making requests in rapid succession, leading to bans or throttling.
    This approach can easily exceed API rate limits, causing request failures or temporary IP bans.

``` py
for _ in range(50):
    response = requests.get('https://jsonplaceholder.typicode.com/posts')
```

- After:
  - Implement automatic backoff with retries based on the API’s rate-limiting headers, ensuring compliance with limits while maximizing data retrieval.
  - This code adapts to the API’s rate limit dynamically, waiting based on the server’s recommended delay, reducing the risk of being banned.

``` py
import time
import requests

def fetch_with_backoff(url, retries=5, backoff_factor=2):
    for attempt in range(retries):
        response = requests.get(url)
        if response.status_code == 200:
            return response.json()
        elif response.status_code == 429:  # Too Many Requests
            retry_after = int(response.headers.get("Retry-After", backoff_factor))
            print(f"Rate limited. Retrying in {retry_after} seconds...")
            time.sleep(retry_after)
        else:
            print(f"Error {response.status_code}: {response.text}")
            break
    return None

data = fetch_with_backoff('https://jsonplaceholder.typicode.com/posts')
```

#### Secure and Scalable API Key Management with Environment Variables

- Before:
  - Beginners often hardcode API keys in the source code, which is insecure
  - Hardcoding keys risks accidental exposure if the code is shared or published.

``` py
api_key = "YOUR_API_KEY"
response = requests.get(f'https://jsonplaceholder.typicode.com/posts?api_key={api_key}')
```

- After:
  - Store API keys in environment variables, using the dotenv package for easy loading in development.
  - Separating secrets from code enhances security and simplifies changes when scaling or managing multiple environments.

``` py
import os
import requests
from dotenv import load_dotenv

load_dotenv()  # Load environment variables from .env file
api_key = os.getenv("API_KEY")

def fetch_with_key(url):
    headers = {'Authorization': f'Bearer {api_key}'}
    response = requests.get(url, headers=headers)
    return response.json()

data = fetch_with_key('<https://jsonplaceholder.typicode.com/posts>')
```

#### Connection Pooling for Optimized Request Performance

- Before:
  - Repeated `requests.get()` calls without pooling waste resources by repeatedly opening and closing connections.

``` py
for _ in range(5):
    response = requests.get('https://jsonplaceholder.typicode.com/posts')
```

- After:
  - Use a Session object to reuse connections, reducing latency and improving efficiency.
  - Reusing connections minimizes overhead, significantly improving performance for high-frequency API requests.

``` py
import requests

session = requests.Session()
for _ in range(5):
    response = session.get('<https://jsonplaceholder.typicode.com/posts>')
    print(response.json())
session.close()
```

#### Robust Retry Strategy with Exponential Backoff

- Before:
  - If a request fails, beginners often retry without delay, which can worsen temporary i
    om requests.adapters import HTTPAdapter

``` py
response = requests.get('https://jsonplaceholder.typicode.com/posts')
```

- After:
  - Implement exponential backoff for retries, which gradually increases delay time, giving the server time to recover.
  - This strategy reduces server load and prevents repeated requests during periods of high latency or temporary downtime.

``` py
from requests.packages.urllib3.util.retry import Retry

session = requests.Session()
retry_strategy = Retry(
    total=5,
    backoff_factor=1,
    status_forcelist=[429, 500, 502, 503, 504],
    method_whitelist=["HEAD", "GET", "OPTIONS"]
)
adapter = HTTPAdapter(max_retries=retry_strategy)
session.mount("https://", adapter)

response = session.get('<https://jsonplaceholder.typicode.com/posts>')
print(response.json())
```

#### Advanced Data Validation with pydantic

- Before:
  - Relying on unchecked data can cause errors if the structure differs from expected.

``` py
data = response.json()
print(data[0]['title'])
```

- After:
  - Use pydantic models to validate and parse responses, ensuring data consistency and catching invalid data structures.
  - Validating with pydantic ensures data integrity, reducing runtime errors caused by unexpected data structures.

``` py
from pydantic import BaseModel, ValidationError
from typing import List

class Post(BaseModel):
    userId: int
    id: int
    title: str
    body: str

try:
    posts = [Post(**post) for post in response.json()]
    for post in posts:
        print(post.title)
except ValidationError as e:
    print(f"Validation error: {e}")
```

#### Smart Caching for Frequent API Calls

- Before: Repeatedly fetching the same data without caching wastes bandwidth and time.

``` py
response = requests.get('<https://jsonplaceholder.typicode.com/posts>')
print(response.json())
```

- After:
  - Use `requests-cache` to cache API responses, reducing unnecessary network calls.
  - Caching improves performance by reducing redundant API calls, which is especially beneficial for static or frequently-accessed data.

``` py
import requests_cache

requests_cache.install_cache('api_cache', expire_after=600)  # Cache expires after 10 minutes
response = requests.get('<https://jsonplaceholder.typicode.com/posts>')
print(response.json())
```

#### Comprehensive Logging for API Monitoring

- Before: Beginners often overlook logging, which makes troubleshooting difficult in production.

``` py
try:
    response = requests.get('<https://jsonplaceholder.typicode.com/posts>')

except requests.RequestException:
    print("Request failed")
```

- After:
  - Implement structured logging to capture error details, which aids debugging and monitoring.
  - Logging provides an error history and facilitates monitoring, making it easier to diagnose issues.

``` py
import logging

logging.basicConfig(filename='api_errors.log', level=logging.ERROR)

try:
    response = requests.get('<https://jsonplaceholder.typicode.com/posts>')
    response.raise_for_status()
except requests.exceptions.RequestException as e:
    logging.error(f"API request failed: {e}")
```

#### Simplified Pagination for Efficient Data Fetching

- Before:
  - Requesting large datasets in a single call can overwhelm the API and increase response time.

``` py
response = requests.get('https://jsonplaceholder.typicode.com/posts')
data = response.json()
```

- After:
  - Fetch data in pages, reducing load on the API and handling large data sets gracefully.
  - Pagination ensures that the application retrieves large datasets efficiently without overwhelming the API server.

``` py
url = 'https://jsonplaceholder.typicode.com/posts'
params = {'_page': 1, '_limit': 10}

while True:
    response = requests.get(url, params=params)
    data = response.json()
    if not data:
        break
    for item in data:
        print(item['title'])
    params['_page'] += 1
```

#### Structured API Documentation for Maintainability

- Before:
  - API requests without documentation make it difficult for other developers to understand and maintain.

``` py
response = requests.get('https://jsonplaceholder.typicode.com/posts')
```

- After:
  - Document functions using clear docstrings to describe request parameters, headers, and
responses.
Proper documentation improves code readability, making it easier for team members to understand and maintain.

``` py
def fetch_posts(page: int, limit: int) -> list:
    """
    Fetches posts from the JSONPlaceholder API with pagination.

    Args:
        page (int): The page number to fetch.
        limit (int): Number of posts per page.

    Returns:
        list: List of post dictionaries.
    """
    url = 'https://jsonplaceholder.typicode.com/posts'
    params = {'_page': page, '_limit': limit}
    response = requests.get(url, params=params)
    return response.json()
```

#### Combining all best practices

``` py
import requests
import os
import requests
from dotenv import load_dotenv
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry
from pydantic import BaseModel, ValidationError
from typing import List
import requests_cache
import logging

logging.basicConfig(filename='api_errors.log', level=logging.ERROR)
load_dotenv()  # Load environment variables from .env file
api_key = os.getenv("API_KEY")
requests_cache.install_cache('api_cache', expire_after=600)  # Cache expires after 10 minutes

class Post(BaseModel):
    userId: int
    id: int
    title: str
    body: str

def fetch_data(url, params, timeout=10), retries=5, backoff_factor=2:
    """
    Fetches posts from the JSONPlaceholder API with pagination.

    Args:
        page (int): The page number to fetch.
        limit (int): Number of posts per page.

    Returns:
        list: List of post dictionaries.
    """
    headers = {'Authorization': f'Bearer {api_key}'}
    session = requests.Session()
    retry_strategy = Retry(
        total=5,
        backoff_factor=1,
        status_forcelist=[429, 500, 502, 503, 504],
        method_whitelist=["HEAD", "GET", "OPTIONS"]
    )
    adapter = HTTPAdapter(max_retries=retry_strategy)
    session.mount("https://", adapter)
    
    for attempt in range(
        try:
            response = session.get(url, params=params, timeout=timeout)
            response.raise_for_status()  # Raises HTTPError for bad responses
            if response.headers.get('Content-Type') == 'application/json; charset=utf-8':
                return response.json()
            else:
                raise ValueError("Unexpected Content-Type in response")
        except requests.exceptions.HTTPError as http_err:
            if response.status_code == 429:  # Too Many Requests
                retry_after = int(response.headers.get("Retry-After", backoff_factor))
                logging.info(f"Rate limited. Retrying in {retry_after} seconds...")
                time.sleep(retry_after)
            else:
                logging.error(f"HTTP error occurred: {http_err}")
                break
        except requests.exceptions.Timeout:
            logging.error("Request timed out.")
        except requests.exceptions.RequestException as err:
            logging.error(f"API request failed: {err}")
        except ValueError as e:
            logging.exception(e)
    session.close()
    return None


url = 'https://jsonplaceholder.typicode.com/posts'
params = {'_page': 1, '_limit': 10}
while True:
    data = fetch_data(url, params=params)
    if not data:
        break
    for item in data:
        print(item['title'])
    params['_page'] += 1

    
    try:
        posts = [Post(**post) for post in data]
        for post in posts:
            print(post.title)
    except ValidationError as e:
        print(f"Validation error: {e}")
```
