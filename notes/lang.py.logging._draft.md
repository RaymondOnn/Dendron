---
id: zd48jq02qht4piif6z0zq36
title: _draft
desc: ''
updated: 1729314017667
created: 1706060336639
---

<https://betterstack.com/community/guides/logging/how-to-start-logging-with-python/#the-python-logging-hierarchy>
<https://www.youtube.com/watch?v=9L77QExPmI0>

## The Components

![Alt text](image-4.png)

### Loggers

- The actual thing used in code to log messages
- Each message is a LogRecord object that contains contextual info i.e. message, level, created, thread
- Loggers also can be configured
  - `level`: to drop message below a certain severity
  - filters: to drop that meet certain conditions or alter messages (for e.g. sensitive data)
- Changes to the messages will persist throughout.
- The LogRecord is then passed to the handlers

#### Log Levels

- Log levels define the severity of the event that is being logged.
- Useful when sieving through large logs for specific events.
- Each log level in Python is associated with a number (from 10 to 50) and has a corresponding module-level method in the logging module as demonstrated in the previous example.
  - `DEBUG (10)`: Detailed information that are useful for diagnosing problems.
  - `INFO (20)`: Confirmation that things are working as expected.
  - `WARNING (30)`: Something unexpected happened, but the system is still working.
  - `ERROR (40)`: A problem that caused part of the system to fail.
  - `CRITICAL (50)`: A serious error, indeicating that the program may be not able to continue running.
- To set the log level:

    ```py
    # By default, the logging module will produce records for
    # events with severity level of WARNING and above.

    import logging

    logging.basicConfig(level=logging.INFO)

    logging.debug("A debug message")
    logging.info("An info message")
    logging.warning("A warning message")
    logging.error("An error message")
    logging.critical("A critical message")
    ```

- To add custom log levels:

    ```py

    import logging


    # Adopted from https://stackoverflow.com/a/35804945/1691778
    # Adds a new logging method to the logging module
    def addLoggingLevel(levelName, levelNum, methodName=None):
        if not methodName:
            methodName = levelName.lower()

        if hasattr(logging, levelName):
            raise AttributeError("{} already defined in logging module".format(levelName))
        if hasattr(logging, methodName):
            raise AttributeError("{} already defined in logging module".format(methodName))
        if hasattr(logging.getLoggerClass(), methodName):
            raise AttributeError("{} already defined in logger class".format(methodName))

        def logForLevel(self, message, *args, **kwargs):
            if self.isEnabledFor(levelNum):
                self._log(levelNum, message, args, **kwargs)

        def logToRoot(message, *args, **kwargs):
            logging.log(levelNum, message, *args, **kwargs)

        logging.addLevelName(levelNum, levelName)
        setattr(logging, levelName, levelNum)
        setattr(logging.getLoggerClass(), methodName, logForLevel)
        setattr(logging, methodName, logToRoot)

    # Create the TRACE level
    addLoggingLevel("TRACE", logging.DEBUG - 5)
    logging.basicConfig(level=logging.TRACE)

    # Use the TRACE level
    logging.trace("A trace message")
    logging.debug("A debug message")
    logging.info("An info message")
    logging.warning("A warning message")
    logging.error("An error message")
    logging.critical("A critical message")

    ```

### Filters

- Filters allow for selectively include or exclude log messages based on specific criteria.
- They implement a single method filter(record), which returns either True to include the log message or False to exclude the log message.

    ```py
    import sys
    import logging


    class LevelFilter(logging.Filter):
        def __init__(self, level):
            self.level = level

        def filter(self, record):
            if record.levelno == self.level:
                return True


    logger = logging.getLogger(__name__)

    stdout = logging.StreamHandler(stream=sys.stdout)

    fmt = logging.Formatter(
        "%(name)s: %(asctime)s | %(levelname)s | %(filename)s%(lineno)s | %(process)d >>> %(message)s"
    )

    level_filter = LevelFilter(logging.WARNING)
    logger.addFilter(level_filter)

    stdout.setFormatter(fmt)
    logger.addHandler(stdout)

    logger.setLevel(logging.INFO)

    logger.info("An info")
    logger.warning("A warning")
    logger.error("An error")
    ```

#### Handlers

- Handlers define how and where to log records i.e. stdout, file, email, log service, network socket, or HTTP API
- Each handler also has level and filters to customize the output
- However, unlike the logger, these changes revert once it is passed to the next handler.

- To use a handler, a Handler object must be instantiated and added to a Logger.
- There are several types of Handler objects
- Multiple Handlers can be added to a single Logger instance so that the logs produced through that Logger can be sent to multiple destinations.

```py
import sys
import logging

logger = logging.getLogger("example")

stdout = logging.StreamHandler(stream=sys.stdout)
stdout.setLevel(logging.INFO) # Note this level filter comes after the main level filter

logger.addHandler(stdout) # add handler to logger

logger.info("An info") # <--- this will be filtered out by main logger unless logger.setLevel(logging.INFO)
logger.warning("A warning")

# Output
A warning
```

#### Formatters

- Log messages are generated in the form of text but LogRecords are python objects
- Formatters is used to convert the LogRecord into text so that it can be sent out
- Formatters allows you to customize how the log message look like and choose which data in included in the log message
- To use a formatter, a formatter object must be instantiated and added to a Logger.
- For more formating options, see [here](https://docs.python.org/3/library/logging.html#logging.Formatter)

```py
import sys
import logging

logger = logging.getLogger("example")

stdout = logging.StreamHandler(stream=sys.stdout)

fmt = logging.Formatter(
    "%(name)s: %(asctime)s | %(levelname)s | %(filename)s:%(lineno)s | %(process)d >>> %(message)s"
)

stdout.setFormatter(fmt)
logger.addHandler(stdout)

logger.setLevel(logging.INFO)

logger.info("An info")
logger.warning("A warning")
```

### The Logging Tree

- Loggers are organized into a tree structure based on their names with the root logger at the root.
- Loggers are accessed and created by name, hence each logger must have a unique name.
- Creating a custom logger is done via the `getLogger()` method and child loggers are created via the dot syntax

    ``` py

    # logger app.example is the child of the app logger
    # which in turn is the child of the "root" logger

    import logging

    app_logger = logging.getLogger("app")
    module_logger = logging.getLogger("app.module")

    print(app_logger.parent)
    print(module.parent)
    # Output
    <RootLogger root (WARNING)>
    <Logger app (WARNING)>
    ```

- Log messages will propagate from child loggers to its parent all the way to the root logger
- Configurations will propagate from parent to child, in the opposite direction

### Best Practices

1. Use `dictConfig`

    - Useful logging is multi-destination
    - Usually want logging to multiple places
        - for e.g.
            - capture all logs in a file on top of having errors go to `stderr`
            - have logs go to stdout and send emails for errors

    ```py
    import logging.config

    logger = logging.getLogger("my_app")

    logging_config = {
        'version': 1,
        'filters': {...},
        'formatters': {...},
        'handlers': {...},
        'loggers': {...},
    }
    ```

**An example: Logging to stdout**

![Alt text](image-3.png)

```py
import logging

logging.info("some_message")   # BAD: This uses the root logger
logger = logging.getlogger("my_app") # GOOD: Use your own logger


# Note: You can store the logging_config separately in a json/yaml file
logging_config = {
    "version": 1,
    "disable_existing_loggers": False,
    "formatters": {
        "simple": {
            "format": "%(levelname)s: %(message)s",  # didnt specify class so uses logging.formatter by default
        }
    },
    "handlers": {
        "stdout": {
            "class": "logging.StreamHandler",
            "formatter": "simple",
            "stream": "ext://sys.stdout",
        }
    },
    "loggers": {
        "root": {"level": "DEBUG", "handlers": ["stdout"]}
    },
}
def setup_logging()
    logging.config.dictConfig(logging_config)
    queue_handler = logging.getHandlerByName("queue_handler")
    if queue_handler is not None:
        queue_handler.listener.start()
        atexit.register(queue_handler.listener.stop())



def main():
    setup_logging()
    logger.debug("debug message", extra={"x": "hello"})
    logger.info("info message")
    logger.warning("warning message")
    logger.error("error message")
    logger.critical("critical message")

...

def main():
    logging.config.dictConfig(config=logging_config)
    ...
```

2. Put all handlers/filters on the root logger

    - An benefit of this is that any messages generated by third party libraries get formatted the same way as messages generated by your own app.

3. Dont use the root logger in your code.

```py
import logging

logging.info("some_message")   # BAD: This uses the root logger
logger = logging.getlogger("my_app")
logger.info("some_message") # GOOD: Use your own logger

```

4. One logger per major subcomponent.

    - Do not use `getLogger(__name__)` in every file

5. Store config in `json` or `yaml` file

6. Use ISO-8601 Timestamps (with timezone)
   ![Alt text](image-1.png)

7. Store persistent logs in JSON for easy search
8. Add Context with `logging.info(..., extra={...})`
9. Log off the main thread
10. For libraries, don't configure logging
