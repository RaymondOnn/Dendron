---
id: e5hngifdh2qjpjbg7vegv63
title: adaptor
desc: ''
updated: 1730393715278
created: 1730393193027
---

- To log extra fields, we need to create a custom formatter that can accept such fields.

    ``` py
    import logging

    # Configure the logger
    _logger = logging.getLogger(__name__)
    _logger.setLevel(logging.DEBUG)

    # Create console handler and set level to debug
    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.DEBUG)

    # Create a formatter that includes the custom 
    # field `app_name` and `version`.
    formatter = logging.Formatter(
        "%(app_name)s - %(version)s - %(levelname)s - %(message)s"
    )
    console_handler.setFormatter(formatter)

    # Add the handler to the logger
    _logger.addHandler(console_handler)
    ```

- To log extra fields in Python using the logging module, we can use the `extra` parameter, which allows us to pass a dictionary of additional context information that can be included in the log message. This dictionary must contain keys that correspond to fields in the log formatter.

    ``` py
    # Log with extra fields
    extra_info = {"app_name": "My App", "version": "1.0.0"}
    logger.warning("This is a warning message.", extra=extra_info)
    # My App - 1.0.0 - WARNING - This is a warning message.

    logger.error("This is an error message.", extra=extra_info)
    # My App - 1.0.0 - ERROR - This is an error message.

    # NOTE: extra_info dictionary required for every logging action else KeyError is raised
    logger.info("This is an info message.") # KeyError: 'app_name'
    ```

- A logger adaptor allows you to inject extra information into log records without modifying the logger configuration every time.

  1. Create a logger using the standard logging module as shown above.
  2. Then we wrap the logger with a LoggerAdapter, which allows you to pass extra context (a dictionary) into the logger.
  3. Log messages as usual, and the adapter will automatically include the extra context.

    ``` py
    # Log with extra fields
    extra_info = {"app_name": "My App", "version": "1.0.0"}

    logger = logging.LoggerAdapter(_logger, extra_info)

    logger.warning("This is a warning message.")
    # My App - 1.0.0 - WARNING - This is a warning message.

    logger.warning("This is an error message.")
    # My App - 1.0.0 - ERROR - This is an error message.
    ```
