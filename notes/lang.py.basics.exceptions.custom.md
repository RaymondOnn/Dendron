---
id: c43t0jjq1wd1wya57dtgux3
title: custom
desc: ''
updated: 1729313553343
created: 1728232145610
---
``` py
import traceback
import sys

class CustomError(Exception):
    def __init__(self, message):
        self.message = message
        current_frame = traceback.extract_tb(sys.exc_info()[2])[-1]
        self.line_number = current_frame.lineno
        self.method_name = current_frame.name

    def __str__(self):
        return f"{self.message} (Line: {self.line_number}, Method: {self.method_name})"

def divide_numbers(a, b):
    if b == 0:
        raise CustomError("Division by zero is not allowed.")
    return a / b

def main():
    try:
        result = divide_numbers(10, 0)
        print(f"Result: {result}")
    except CustomError as e:
        print(f"Error: {e}")
        
if __name__ == '__main__':
    main()

>>> Error: Division by zero is not allowed. (Line: 5, Method: divide_numbers)
```