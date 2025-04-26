---
id: vyz8blf6tc50ect6q4flmbl
title: logging
desc: ''
updated: 1732454055370
created: 1732453917073
---

<https://github.com/joblib/joblib/issues/1017>

``` py
import logging
from logging.handlers import QueueHandler, QueueListener
from multiprocessing import Manager
from random import random
from time import sleep

from joblib import Parallel, delayed

log = logging.getLogger(__name__)


def configure_root_logger():
    root = logging.getLogger()
    console_handler = logging.StreamHandler()
    formatter = logging.Formatter('%(asctime)s %(processName)-10s %(name)s %(levelname)-8s %(message)s')
    console_handler.setFormatter(formatter)
    root.addHandler(console_handler)
    root.setLevel(logging.DEBUG)
    return root


def configure_worker_logger(log_queue, log_level):
    worker_logger = logging.getLogger('worker')
    if not worker_logger.hasHandlers():
        h = QueueHandler(log_queue)
        worker_logger.addHandler(h)
    worker_logger.setLevel(log_level)
    return worker_logger


def process(x, log_queue, log_level):
    worker_logger = configure_worker_logger(log_queue, log_level)
    for i in range(3):
        sleep(random())
        worker_logger.debug(f'Logging a number {x}, try {i}')


def main():
    root_logger = configure_root_logger()
    m = Manager()
    q = m.Queue()
    listener = QueueListener(q, *root_logger.handlers)  # Could also assign the handlers of a different logger here
    listener.start()
    log.info("Starting number generation")
    Parallel(n_jobs=4)(delayed(process)(i, log_queue=q, log_level=root_logger.getEffectiveLevel()) for i in range(20))
    log.info("Finished number generation")
    listener.stop()


if __name__ == '__main__':
    main()
```

- As a decorator

``` py
import logging
from logging.handlers import QueueHandler, QueueListener
from multiprocessing import Manager
from random import random
from time import sleep
from joblib import Parallel, delayed

log = logging.getLogger(__name__)


def configure_root_logger():
    root = logging.getLogger()
    console_handler = logging.StreamHandler()
    file_handler = logging.FileHandler("joblib.log")
    formatter = logging.Formatter("%(asctime)s %(processName)-10s %(name)s %(levelname)-8s %(message)s")
    file_handler.setFormatter(formatter)
    console_handler.setFormatter(formatter)
    root.addHandler(file_handler)
    root.addHandler(console_handler)
    root.setLevel(logging.DEBUG)
    return root


def configure_worker_logger(log_name="Default", log_level="DEBUG"):
    def decorator(func: callable):
        def wrapper(*args, **kwargs):
            worker_logger = logging.getLogger(log_name)
            if not worker_logger.hasHandlers():
                h = QueueHandler(kwargs["log_queue"])
                worker_logger.addHandler(h)
            worker_logger.setLevel(log_level)
            kwargs["log_queue"] = worker_logger
            return func(*args, **kwargs)

        return wrapper

    return decorator


@configure_worker_logger(log_name="process_1", log_level="INFO")
def process_1(x, log_queue, **kwargs):
    for i in range(3):
        sleep(random())
        log_queue.info(f"process_1 info printing {x}, try {i}")
        log_queue.error(f"Logging a process_1 function {x}, try {i}")


@configure_worker_logger(log_name="process_2", log_level="WARNING")
def process_2(x, log_queue, **kwargs):
    for i in range(3):
        sleep(random())
        log_queue.info(f"process_2 info printing {x}, try {i}")
        log_queue.error(f"Logging a process_2 function {x}, try {i}")


if __name__ == "__main__":
    root_logger = configure_root_logger()
    m = Manager()
    q = m.Queue()
    listener = QueueListener(q, *root_logger.handlers)  # Could also assign the handlers of a different logger here
    listener.start()
    log.info("Starting process_1 generation")
    Parallel(n_jobs=4)(delayed(process_1)(i, log_queue=q) for i in range(2))
    log.info("Finished process_1 generation")

    log.info("Starting process_2 generation")
    Parallel(n_jobs=4)(delayed(process_2)(i, log_queue=q) for i in range(2))
    log.info("Finished process_2 generation")

    listener.stop()

```
