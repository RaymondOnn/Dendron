---
id: v7cvj0jj22zj0fcd18whpgg
title: color
desc: ''
updated: 1730393174362
created: 1730381599184
---
<https://python.plainenglish.io/a-simple-solution-to-print-logs-with-color-in-python-587fc6623dad>

``` py
from enum import StrEnum
import logging

class Colors(StrEnum):
    """An Enum for colors using ANSI escape sequences.

    Reference:
    - https://stackoverflow.com/questions/287871
    """

    DEBUG = "\033[90m"  # Gray
    WARNING = "\033[93m"  # Yellow
    ERROR = "\033[91m"  # Red
    CRITICAL = "\033[35m"  # Magenta
    RESET = "\033[0m"  # Resets the color to default.
    

class CustomFormatter(logging.Formatter):
    def format(self, record):
        color = ""
        if record.levelname in Colors.__members__:
            color = Colors[record.levelname]

        # Note: Rmb to reset the color, else the color will persist until it's changed
        color_formatter = color + "%(levelname)s: %(message)s" + Colors.RESET
        formatter = logging.Formatter(color_formatter)
        return formatter.format(record)

# Configure logger
logger = logging.getLogger("Colorful logger")
logger.setLevel(logging.DEBUG)

handler = logging.StreamHandler()
handler.setFormatter(CustomFormatter())
logger.addHandler(handler)

logger.debug("This is some debug message.")
logger.info("This is some info.")
logger.warning("This is some warning.")
logger.error("This is some error.")
logger.critical("This is some critical error.")

```
