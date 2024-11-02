---
id: aougcham9gq4zcjeqylzn0q
title: messages
desc: ''
updated: 1729315297113
created: 1729314232002
---

### Writing Better Error Messages

#### Be Specific and Descriptive

- Avoid vague error messages like "Something went wrong" or "Invalid input".
- Instead, explain exactly what happened and why it’s an issue.

    ``` py
    # When you're validating user input, you could write:
    raise ValueError("Invalid age: Age cannot be negative")
    ```

This message provides clear information about the nature of the error and helps the developer understand what went wrong.

#### Expected vs. Actual Values

- Useful to provide context by including both what you expected and what you received.
- This gives the user a much clearer picture of the problem.

    ``` py
    raise TypeError(f"Expected an integer but got {type(input_value).__name__}")
    ```

By including the actual type of input, the developer immediately knows what went wrong without having to guess.

#### Guide the user Toward a Solution

- Sometimes, error messages should do more than point out the problem — they should help solve it.
- If possible, suggest a fix or guide the user on how to avoid the error. For example:

    ``` py
    raise ValueError("Invalid date format. Please use 'YYYY-MM-DD'")
    ```

This message not only explains the error but also tells the user how to provide the correct format, reducing future errors.

#### Avoid Overly Technical Language

- Overly technical language can confuse users.
- Use simple, straightforward language that makes sense to both experienced developers and beginners.

    ```py
    # Instead of saying:
    raise Exception("Memory allocation error during heap resizing")

    # You could write
    raise MemoryError("Failed to allocate enough memory. Try reducing input size.")
    ```

This message is easier to understand and suggests a practical next step.

#### Leverage Built-in Python Exceptions

- Python provides a wide range of built-in exceptions that cover many common issues.
- Instead of using a generic Exception, raise specific exceptions like ValueError, KeyError, or TypeError where appropriate.
- This makes the error messages more meaningful and easier to handle programmatically.

    ``` py
    raise KeyError(f"The key '{key}' was not found in the dictionary")
    ```

Using built-in exceptions also allows users to catch specific types of errors, which improves code readability and robustness.

#### Add Context with Tracebacks

- Tracebacks are a powerful tool in Python for understanding where and why an error occurred.
- Custom error messages can obscure the traceback if not used carefully.
- When creating custom exceptions, try to preserve the original traceback to avoid losing valuable debugging information.
- One way to do this is by using from when chaining exceptions:

    ``` py
    try:
        # some code
    except ValueError as e:
        raise MyCustomError("Custom message") from e
    ```

This keeps the original error’s context intact while still providing a custom message.

#### Don’t Overload Error Messages

- Keep your error messages short and to the point.
- If you overload them with too much information, they can become difficult to read.
- Aim for clarity and avoid including unnecessary technical jargon or debug info that the user doesn’t need.

``` py
# BAD
raise Exception("""
    The operation failed due to an unexpected error. 
    Please check tour input parameters and ensure that they are valid. 
    If the issue persists, please contact technical support with the following error code: 403E2704
""")

# BETTER
raise Exception(:Invalid Input. Please double-check the values you entered and try again.")
```

#### Use Logging for Detailed Information

- If you need to include detailed diagnostic information, consider logging it rather than including it in the error message.
- The error message can remain clean and concise, while logs can provide all the gritty details for more in-depth debugging.

    ``` py
    import logging
    logger = logging.getLogger(__name__)
    try:
        # some code
    except Exception as e:
        logger.error("Detailed log message for debugging", exc_info=True)
        raise RuntimeError("An unexpected error occurred. Please contact support.")
    ```

This allows you to maintain clean error messages while still offering detailed logs when needed.

### Test Your Error Messages

- Just as you would test your code to ensure it works as expected, you should test that your error messages are clear, useful, and informative.
- Always ask yourself: “If I encountered this error message, would I know how to fix it? If the answer is no, it’s time for a revision.”
