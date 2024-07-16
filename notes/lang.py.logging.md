---
id: cloahqwzuqhtkzbzm3urmrf
title: logging
desc: ''
updated: 1720848662020
created: 1692039181977
---
https://www.python-engineer.com/courses/advancedpython/10-logging/
https://towardsdatascience.com/basic-to-advanced-logging-with-python-in-10-minutes-631501339650
<https://betterstack.com/community/guides/logging/how-to-start-logging-with-python/>

- https://www.loggly.com/blog/exceptional-logging-of-exceptions-in-python/
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

### Types of Logs

#### Task Logs

- These logs are to record down important information throughout the execution of the task. We don’t want to print every line of code we run, but there should be sufficient logs to help us navigate through the task when we look at them.
- Logs act like a breakpoint during debugging. They are useful for us to determine the source of error during task failures.

In this extraction job, we log the query and connection.

- This achieves two things
  - Informs us that the task is running fine until each point
  - Shows us the query and connection variable that is passed
If something goes wrong, we can clearly determine which step is faulty, as well as check if the query or connection variable is faulty.

#### Job Logs

- Job logs are records of job runs.
- At the very least, they show the start to end time and status of the job.
- If your workflow management system does not already have this by default, you should have this stored somewhere, maybe even in your data warehouse.
- Create an audit table in your data warehouse. Insert a row for every job run with these data. Data Scientists and Analysts can easily navigate through the table to check the latest job run for certain tables, and if the job runs successfully.


- Code for logging to look nice
``` py
import sys

def load_logger(verbose: bool = False) -> None:  # pragma no cover
    """Configure the Logging logger.

    Args:
        verbose: Set the logging level to Debug.
    """
    logging.addLevelName(logging.INFO, "\033[36mINFO\033[0m   ")
    logging.addLevelName(logging.ERROR, "\033[31mERROR\033[0m  ")
    logging.addLevelName(logging.DEBUG, "\033[32mDEBUG\033[0m  ")
    logging.addLevelName(logging.WARNING, "\033[33mWARNING\033[0m")


    if verbose:
        logging.basicConfig(
            format="%(asctime)s %(levelname)s %(name)s: %(message)s",
            stream=sys.stderr,
            level=logging.DEBUG,
            datefmt="%Y-%m-%d %H:%M:%S",
        )
        logging.getLogger("sh").setLevel(logging.WARN)
    else:
        logging.basicConfig(
            stream=sys.stderr, level=logging.INFO, format="%(levelname)s %(message)s"
        )
```