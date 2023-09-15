---
id: cloahqwzuqhtkzbzm3urmrf
title: Logging
desc: ''
updated: 1694226747285
created: 1692039181977
---

https://betterstack.com/community/guides/logging/how-to-start-logging-with-python/
- [Logging into MYSQL](https://www.youtube.com/watch?v=9fnylVX8LU0)
```py
import logging

logging.basicConfig(
    filename='sraper.log', 
    level=logging.DEBUG, 
    format='%(asctime)s - %(message)s',
    datefmt= '%d-%b-%y %H:%M:%S'
) 

```