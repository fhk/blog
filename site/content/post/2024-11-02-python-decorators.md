---
title: "Understanding Python Decorators: A Practical Guide"
date: 2025-11-05T00:00:00Z
description: "Learn how Python decorators work with practical examples and use cases"
tags: ["python", "tutorial", "programming"]
categories: ["Tutorials"]
---

Python decorators are a powerful feature that allows you to modify or enhance functions and methods. Let's dive into how they work and when to use them.

## What Are Decorators?

A decorator is a function that takes another function as input and extends its behavior without explicitly modifying it. They're commonly used for:

- Logging
- Authentication/authorization
- Caching/memoization
- Rate limiting
- Input validation

## Basic Decorator Example

Here's a simple decorator that logs function calls:

```python
def log_calls(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

@log_calls
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
```

Output:
```
Calling greet
Hello, Alice!
Finished greet
```

## How It Works

When you use `@log_calls`, Python does this behind the scenes:

```python
greet = log_calls(greet)
```

The decorator wraps the original function with additional functionality.

## Decorators with Arguments

You can create decorators that accept arguments:

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(times=3)
def say_hello():
    print("Hello!")

say_hello()
```

Output:
```
Hello!
Hello!
Hello!
```

## Practical Example: Timing Decorator

Here's a useful decorator for measuring function execution time:

```python
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
    return "Done!"

result = slow_function()
```

**Note:** Always use `@wraps(func)` from `functools` to preserve the original function's metadata.

## Class-Based Decorators

You can also create decorators using classes:

```python
class CountCalls:
    def __init__(self, func):
        self.func = func
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        print(f"Call {self.count} to {self.func.__name__}")
        return self.func(*args, **kwargs)

@CountCalls
def process_data():
    print("Processing...")

process_data()  # Call 1
process_data()  # Call 2
```

## Common Built-in Decorators

Python provides several useful built-in decorators:

- `@property` - Create getter methods
- `@staticmethod` - Define static methods in classes
- `@classmethod` - Define class methods
- `@functools.lru_cache` - Memoize function results

## Best Practices

1. Use `@functools.wraps` to preserve function metadata
2. Keep decorators simple and focused
3. Document decorator behavior clearly
4. Consider performance implications
5. Chain decorators carefully (order matters!)

## Conclusion

Decorators are a elegant way to modify function behavior in Python. They promote code reuse and separation of concerns. Start using them in your projects to write cleaner, more maintainable code!

---

*Have questions or suggestions? Let me know in the comments!*
