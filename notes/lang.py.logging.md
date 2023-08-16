---
id: cloahqwzuqhtkzbzm3urmrf
title: Logging
desc: ''
updated: 1692063964370
created: 1692039181977
---

https://betterstack.com/community/guides/logging/how-to-start-logging-with-python/

```py
import logging

logging.basicConfig(
    filename='sraper.log', 
    level=logging.DEBUG, 
    format='%(asctime)s - %(message)s',
    datefmt= '%d-%b-%y %H:%M:%S'
) 

```