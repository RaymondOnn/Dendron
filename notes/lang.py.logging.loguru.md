---
id: rne0nn8rn4lflzym7d4rblc
title: loguru
desc: ''
updated: 1715163155406
created: 1715093805039
---
https://betterstack.com/community/guides/logging/loguru/#migrating-from-logging-to-loguru

## `loguru`

### Getting Started

- To install loguru:
    ``` py
    pip install loguru
    ```
- The most basic way to use Loguru is by importing the logger object from the loguru package. 
    - This logger is pre-configured with a handler that logs to the `stderr` by default. 
    - `debug()`: for logging a message at the `DEBUG` level.
        ``` py
        from loguru import logger

        logger.debug("Happy logging with Loguru!")
        
        # Output: 
        # timestamp | log level | file location, scope, lineno | log message 
        >>> 2022-08-10 11:16:59.511 | DEBUG | __main__:<module>:3 - Happy logging with Loguru!
        ```

### Log levels in Loguru
- Log levels specify the severity of a log record.
- Often used to filter messages based on how urgent they are. 
- Seven unique log levels, and each associated with a number:
  - `TRACE (5)`: for fine-grained information about the program's execution path for diagnostic purposes.
  - `DEBUG (10)`: used by developers to record messages for debugging purposes.
  - `INFO (20)`: for informational messages that describe the normal operation of the program.
  - `SUCCESS (25)`: similar to INFO but for indicating the success of an operation.
  - `WARNING (30)`: for indicating an unusual event that may require further investigation.
  - `ERROR (40)`: for error conditions that affected a specific operation.
  - `CRITICAL (50)`: for error conditions that prevent a core function from working.
- Each log level also have a corresponding method to send log records with that log level:
    ``` py
    logger.trace("A trace message.")
    logger.debug("A debug message.")
    logger.info("An info message.")
    logger.success("A success message.")
    logger.warning("A warning message.")
    logger.error("An error message.")
    logger.critical("A critical message.")
    
    # Output:
    # These messages are printed to the console in different colors based on their log level.

    # NOTE: The `TRACE` level message is not included in the output since 
    #   Loguru defaults to using DEBUG as its minimum level. 
    #   Any logs with a severity lower than DEBUG are ignored.
    >>> 2022-08-10 11:58:33.224 | DEBUG | __main__:<module>:12 - A debug message.
    >>> 2022-08-10 11:58:33.224 | INFO | __main__:<module>:13 - An info message.
    >>> 2022-08-10 11:58:33.225 | SUCCESS | __main__:<module>:14 - A success message.
    >>> 2022-08-10 11:58:33.226 | WARNING | __main__:<module>:15 - A warning message.
    >>> 2022-08-10 11:58:33.226 | ERROR | __main__:<module>:16 - An error message.
    >>> 2022-08-10 11:58:33.227 | CRITICAL | __main__:<module>:17 - A critical message.
    ```

#### Changing log level
- `add(..., level=<LOG_LEVEL>)`: for changing the default level
    ``` py
    import sys
    from loguru import logger
    
    # Remove the config for the default handler (whose ID is 0)
    logger.remove(0)

    # adds a new handler to the logger. 
    # This handler logs to the stderr and only records logs with INFO severity or greater.
    logger.add(sys.stderr, level="INFO")
    ```

#### Creating custom levels
- `level(name, no, color, icon)`:  create custom log levels
    - `name`: the name of the log level
    - `no`: the corresponding severity value (must be an integer).
    - `color`: the color markup.
    - `icon`: the icon of the level.

    ``` py
    import sys
    from loguru import logger

    # adds the FATAL log level to the logger
    logger.level("FATAL", no=60, color="<red>", icon="!!!")
    logger.log("FATAL", "A user updated some information.")
    ```
    - For custom log levels, log using the generic `log(<LOG_LEVEL_NAME>, <LOG_MESSAGE>)` method on the logger 

### Customizing Loguru
- `add(<SINK>, <LEVEL>, <FORMAT>)`: create custom handlers, formatters, and filters to customize a logger's formatting and output. 
    - `sink`: specifies destination for log records produced bu the logger. Defaults to `sys.stderr`.
    - `level`: specifies the minimum log level for the logger.
    - `format`: useful for defining a custom format for your logs.
        ``` py
        import sys
        from loguru import logger

        logger.remove()
        logger.add(sys.stderr, format="{time} | {level} | {message}")

        logger.debug("Happy logging with Loguru!")
        ```
        - Each log record in Loguru is a Python dictionary, which contains data such as its timestamp, log level, and more.
        - `format` defines the custom format, which takes three directives in this example:
          - {time}: the timestamp,
          - {level}: the log level,
          - {message}: the log message.

        - Some of these directives also support further customization. 
         
            ``` py
            # the time directive can be changed to a more human-readable format
            logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss} | {level} | {message}")
            # Output:
            August 9, 2022 > 15:35:01 | DEBUG | Happy logging with Loguru!
            
            # To use UTC instead, add !UTC at the end of the time format:
            logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss!UTC} | {level} | {message}")
            # Output
            August 9, 2022 > 19:35:01 | DEBUG | Happy logging with Loguru!
            ```
    - `filter`: used to determine whether a record should be logged or not.
        ``` py

        import sys
        from loguru import logger
        
        # filtering for WARNING level messages
        def level_filter(level):
            def is_level(record):
                return record["level"].name == level
            return is_level

        logger.remove(0)
        logger.add(sys.stderr, filter=level_filter(level="WARNING"))
        ```
    - `colorize`: takes a boolean value and determines whether or not terminal colorization should be enabled.
    - `serialize`: causes the log record to be presented in **JSON format** if set to True.
        - With structured logging in JSON format, it allows for easy parsing and analysis since the information in each record will be provided in key/value pairs.
        ``` py
        import sys
        from loguru import logger

        logger.remove(0)
        logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss!UTC} | {level} | {message}", serialize=True)
        logger.debug("Happy logging with Loguru!")
        
        # Output
        {
            "text": "August 10, 2022 > 19:38:06 | DEBUG | Happy logging with Loguru!\n", 
            "record": {
                "elapsed": {"repr": "0:00:00.004000", "seconds": 0.004}, 
                "exception": null, 
                "extra": {}, 
                "file": {
                    "name": "app.py", 
                    "path": "C:\\Users\\Eric\\Documents\\Better Stack\\loguru-demo\\app.py"
                }, 
                "function": "<module>", "level": {"icon": "🐞", "name": "DEBUG", "no": 10}, 
                "line": 8, 
                "message": "Happy logging with Loguru!", 
                "module": "app", 
                "name": "__main__", 
                "process": {"id": 22652, "name": "MainProcess"}, 
                "thread": {"id": 25892, "name": "MainThread"}, 
                "time": {"repr": "2022-08-10 15:38:06.369578-04:00", "timestamp": 1660160286.369578}
            }
        }
        ```
        - If you don't need to include everything shown above in the log record, you can create a custom `serialize()` function and use it as follows:

            ``` py
            import sys
            import json
            from loguru import logger


            # Selecting only timestamp, message and level 
            def serialize(record):
                subset = {
                    "timestamp": record["time"].timestamp(),
                    "message": record["message"],
                    "level": record["level"].name,
                }
                return json.dumps(subset)


            # Update the record["extra"] dictionary 
            def patching(record):
                record["extra"]["serialized"] = serialize(record)


            logger.remove(0)

            logger = logger.patch(patching) # <--- apply to record dictionary
            logger.add(sys.stderr, format="{extra[serialized]}")
            logger.debug("Happy logging with Loguru!")

            # Output
            {"timestamp": 1663328693.765488, "message": "Happy logging with Loguru!", "level": "DEBUG"}
            ```
    - `backtrace`: determines whether the exception trace should extend beyond the point where the error is captured, making it easier to debug.
    - `diagnose`: determines whether the variable values should be displayed in the exception trace. You should set it to False in the production environment to avoid leaking sensitive information.
    - `enqueue`: enabling this option places the log records in a queue to avoid conflicts when multiple processes are logging to the same destination.
    - `catch`: if an unexpected error happens when logging to the specified sink, you can catch that error by setting this option to True. The error will be printed to the standard error.


 
#### Adding contextual data to your logs
- Besides the log message, it is often necessary to include other relevant information in the log entry so that you can use such data to filter or correlate your logs.
    - For e.g. if you are running an online shopping platform and a seller updates one of their products, you should include the seller and product ID in the log entry 
- **NOTE**: Ensure that the `{extra}` directive is included in your custom format. This is a dictionary where the contextual data is stored for each log entry (if any).
    ``` py
    logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss} | {level} | {message} | {extra}")
    ```
- You can subsequently use either `bind()` or `contextualize()` to include extra information at log point.
    - `bind()`: returns a child logger that inherits any existing contextual data from its parent and creates a custom context at that is subsequently included with all the records produced by the logger.

        ``` py
        import sys
        from loguru import logger

        logger.remove(0)
        logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss} | {level} | {message} | {extra}")

        childLogger = logger.bind(seller_id="001", product_id="123")
        childLogger.info("product page opened")
        childLogger.info("product updated")
        childLogger.info("product page closed")

        logger.info("INFO message")
        
        # Output
        September 16, 2022 > 13:04:10 | INFO | product page opened | {'seller_id': '001', 'product_id': '123'}
        September 16, 2022 > 13:04:10 | INFO | product updated | {'seller_id': '001', 'product_id': '123'}
        September 16, 2022 > 13:04:10 | INFO | product page closed | {'seller_id': '001', 'product_id': '123'}
        
        # Notice that the bind() method does not affect the original logger,
        September 16, 2022 > 13:06:08 | INFO | INFO message | {}
        ```
        - To override the parent logger instead, assign it to `logger.bind()` 

            ``` py
            logger = logger.bind(seller_id="001", product_id="123")
            ```
    - `contextualize()`: modifies its extra dictionary directly without returning a new logger. 
        - Requires the use of `with` statement:

        ``` py
        import sys
        from loguru import logger

        logger.remove(0)
        logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss} | {level} | {message} | {extra}")

        def log():
            logger.info("A user requested a service.")


        with logger.contextualize(seller_id="001", product_id="123"):
            log()
        Output
        August 12, 2022 > 11:00:52 | INFO | A user requested a service. | {'seller_id': '001', 'product_id': '123'}
        ```
### Logging errors with Loguru: `catch()`
- `catch()`: log errors when they happen inside a function:

    ``` py
    import sys
    from loguru import logger

    logger.remove(0)
    logger.add(sys.stderr, format="{time:MMMM D, YYYY > HH:mm:ss} | {level} | {message} | {extra}")

    def test(x):
        50/x

    with logger.catch(): # <---- captures and logs any exception
        test(0)

    # Output
    # info included: timestamp | log level | log message | stack trace | type of error
    August 29, 2022 > 12:11:15 | ERROR | An error has been caught in function '<module>', process 'MainProcess' (70360), thread 'MainThread' (4380231040): | {}
    Traceback (most recent call last):
    > File "/Users/erichu/Documents/Better Stack/loguru-demo/app.py", line 25, in <module>
        test(0)
        └ <function test at 0x10593f760>
    File "/Users/erichu/Documents/Better Stack/loguru-demo/app.py", line 22, in test
        50/x
        └ 0
    ZeroDivisionError: division by zero
    ```
    - You can also use a decorator  instead of with statement:

        ``` py
        @logger.catch
        def test(x):
            50/x

        test(0)
        ```
    - `catch()` can also take the following parameters:

        - `exception`: specifies one or more exception types  that should be intercepted by the catch() method.
        - `level`: overwrites the default level for errors (ERROR).
        - `reraise`: determines whether the exception should be raised again after being logged.
        - `onerror`: defines a callback function that will be executed when an error has been caught.
        - `exclude`: creates a blocklist of exception types that should not be caught and logged by the catch() method.
        - `default`: defines the value to be returned if an error occurred in the decorated function without being re-raised.
        - `message`: overrides the default error message.

        ``` py
        # Changing the level and message of a logged error:
        
        # Now errors in the test() function will now be logged at the CRITICAL level with a custom message:
        @logger.catch(level="CRITICAL", message="An error caught in test()")
        def test(x):
            50/x

        test(0)
        
        # Output
        September 20, 2022 > 13:08:01 | CRITICAL | An error caught in test() | {}
        Traceback (most recent call last):

        > File "/Users/erichu/Documents/Better Stack/loguru/app.py", line 12, in <module>
            test(0)
            └ <function test at 0x101dd9b40>

        File "/Users/erichu/Documents/Better Stack/loguru/app.py", line 10, in test
            50/x
            └ 0

        ZeroDivisionError: division by zero
        ```
- `exception()`: for logging exceptions at the ERROR level

    ``` py
    try:
        1/0
    except Exception as e:
        logger.exception(e)
    ```
### Logging to files
- The `sink` option allows you to choose the destination of all log records emitted through a logger. 

    ``` py
    # pushing log messages to a local file, loguru.log
    logger.add("loguru.log")

    logger.debug("A debug message.")
    ```

- When sink is pointing to a file, the `add()` method provides a few more options for customizing how the log file should be handled:
    - `rotation`: 
        - specifies a condition in which the current log file will be closed and a new file will be created. 
        - This condition can be `int` / `datetime` / `str`.
            - `int`: for max bytes the current file is allowed to hold before a new one is created. 
            - `datetime.timedelta`: for the frequency of each rotation
            - `datetime.time`:  for the time of the day each rotation should occur. 
            - `str` for the human-friendly variant of the aforementioned types. (recommended)
        ``` py
        # log rotation occurs every five seconds.
        # file renamed to loguru.<timestamp>.log once done with the file
        logger.add("loguru.log", rotation="5 seconds")
        
        # files older than one minute will be removed automatically
        logger.add("loguru.log", rotation="5 seconds", retention="1 minute")
        
        # only the three newest files will be retained. 
        logger.add("loguru.log", rotation="5 seconds", retention=3)
        ```
    - `retention`: specifies how long each log file will be retained before it is deleted from the filesystem.
    - `compression`: the log file will be converted to the specified compression format if this option is set.
    - `delay`: if set to True, the creation of a new log file will be delayed until the first logmessage is pushed.
    - `mode, buffering, encoding`: These parameters will be passed to Python's open() function which determines how Python will open the log files.



### Migrating from logging to Loguru
1. When you are using the logging module, it is common to use the getLogger() function to initialize a logger. 
   - With Loguru, only need to import the logger
   - Each time this imported logger is used, it will **automatically contain the contextual `__name__` value**.

    ``` py
    # Using Python default logger
    logger = logging.getLogger('my_app')

    # Using Loguru. This is sufficient, the logger is ready to use.
    from loguru import logger
    ```
2. When using the logging module, you need to set up a Handler, Filter, Formatter, and other related objects to configure the logger. In Loguru, you only need to use the `add()` method so you can replace:

    ``` py
    # Using Python default logger
    logger.setLevel(logging.INFO)

    formatter = logging.Formatter(. . .)
    handler = logging.Handler(. . .)
    handler.setFormatter(formatter)

    filter = logging.Filter(. . .)
    . . .
    logger.addHandler(handler)
    logger.addFilter(filter)

    # Using loguru
    logger.add(sink=. . ., level='. . .', format='. . .', filter=. . .)
    ```
- Regarding formatting, if using `%` style parameter with the `Formatter` object, replace them with `{}` style format 
  - replace `%(username)s` with `{username}`
  - replace `logger.debug("User: %s", username)` with `logger.debug("User: {}", username)`
  

- NOTE: Loguru is also fully compatible with existing Handler objects created using the logging module so it is possible to add them directly. 

    ``` py
    from loguru import logger
    import logging

    handler = logging.FileHandler(filename='my_app.log')

    logger.add(handler)
    ```
> See the Loguru migration guide  more details regarding switching from the standard logging module.

