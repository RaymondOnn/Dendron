---
id: 4en68jhsiay8lpcec6clbuw
title: Railway
desc: ''
updated: 1693805788828
created: 1693805740760
---
## Monadic Error Handling

### Monads: Separate business logic from the error-handling logic

### Railway Oriented Programming



- Coming from the functional programming paradigm
- Instead of using exception, the function can instead return either of two values
  - 'Success' object, containing the result of the computation
  - 'Failure' object, containing the error that occurred
- When chaining functions, each function should then
  - output either 'Success' or 'Failure'
  - accept 'Success' or 'Failure' as inputs
- In essence, there will be two tracks, a 'Success' track and a 'Failure' track flow through the program
  - If everything goes well, you stay on the 'Success' track
  - But once error occurred, you shift to the 'Failure' track

#### bind: Adapting single track switches to two tracks

```py
def bind(fx):
    def adapt(status_object):
        if isinstance(status, 'Success'):
            return fx(status.value)    # Success / Failure are objects
        else: 
            return status_object  # Failure
        return adapt

update_db_after = bind(update_db_before)
```

#### map: Making single track-function two tracks

```py
def map(fx):
    def adapt(status_object):
        if isinstance(status, Success):
            # note this is compose(fx, Success)
            return Success(fx(status_object.value))    
        else: 
            return status_object  # Failure
        return adapt

# after refactoring
def map(fx):
    return bind(compose(fx, Success))       
```

#### Implementation Example

```py
# package that supports railway programming
# docs: https://github.com/dry-python/returns
pip install returns
```

<br>

```py
import sqlite3
from returns.result import Result, safe
from returns.pipeline import flow
from returns.pointfree import bind

class SQLite():
    def __init__(self, file='application.db'):
        self.file = file
        self.conn = None
    def __enter__(self):
        self.conn = sqlite3.connect(self.file)
        return self.conn.cursor()
    def __exit__(self, type, value, traceback):
        if self.conn: self.conn.close()
    
class NotFoundError(Exception):
    pass

class NotAuthorizedError(Exception):
    pass

def fetch_blog(blog_id) -> Result['Blog', Exception]:
    return flow(
        blog_id,
        fetch_blog_from_db,
        bind(blog_to_dict),
        bind(verify_access)
    )

@safe     # converts exception into either Success / Failure
def fetch_blog_from_db(blog_id):
    """Fetches blog from SQLite3 database."""
    with SQLite('application.db') as cur:
        cur.execute(f"SELECT * FROM blogs where id=?", [blog_id])
        result = cur.fetchone()
        if result is None:
            raise NotFoundError(f'Unable to find blog with id {blog_id}.')
        return result

@safe
def blog_to_dict(item) -> 'Blog':
    """Convert SQLite result to dictionary."""
    return { 
        'id': item[0],
        'published': item[1],
        'title': item[2],
        'content': item[3],
        'public': bool(item[4])
         }

@safe
def verify_access(blog) -> 'Blog':
    """Check that blog is accessible."""
    blog_id = blog['id']
    blog_public = blog['public']
    if not blog_public:
        raise NotAuthorizedError(f'You are not allowed to access blog with id {blog_id}.')
    return blog

res = fetch_blog("first-blog")
print(res)
```

#### Other Resources
- talk on railway programming: https://www.youtube.com/watch?v=cKixdve3JGg&ab_channel=PyData