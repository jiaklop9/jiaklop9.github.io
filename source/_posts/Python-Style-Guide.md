---
title: Python Style Guide
date: 2025-05-27 10:11:07
tags: python
categories: python
---

# Python Code Style Guide

## Table of Contents
1. [General Principles](#general-principles)
2. [Code Layout](#code-layout)
3. [Naming Conventions](#naming-conventions)
4. [Comments & Documentation](#comments--documentation)
5. [Functions & Classes](#functions--classes)
6. [Imports](#imports)
7. [Error Handling](#error-handling)
8. [Best Practices](#best-practices)

## General Principles

- **Readability counts**: Code is read more often than it's written
- **Consistency is key**: Follow established patterns within the codebase
- **Explicit is better than implicit**: Make your intentions clear
- **Simple is better than complex**: Favor straightforward solutions

## Code Layout

### Indentation
- Use **4 spaces** per indentation level
- Never mix tabs and spaces

```python
# Good
def calculate_total(items):
    total = 0
    for item in items:
        total += item.price
    return total

# Bad
def calculate_total(items):
  total = 0
  for item in items:
      total += item.price
  return total
```

### Line Length
- Limit lines to **88 characters** (Black formatter standard)
- Break long lines using parentheses for natural grouping

```python
# Good
result = some_function(
    argument_one,
    argument_two,
    argument_three
)

# Good
if (condition_one and condition_two and
    condition_three):
    do_something()

# Bad
result = some_function(argument_one, argument_two, argument_three, argument_four)
```

### Blank Lines
- **2 blank lines** around top-level functions and classes
- **1 blank line** around method definitions inside classes
- Use blank lines sparingly within functions to separate logical sections

```python
class UserManager:
    """Manages user operations."""
    
    def __init__(self):
        self.users = []
    
    def add_user(self, user):
        """Add a user to the system."""
        self.users.append(user)


def process_data(data):
    """Process incoming data."""
    cleaned_data = clean_data(data)
    
    results = []
    for item in cleaned_data:
        processed_item = transform_item(item)
        results.append(processed_item)
    
    return results
```

## Naming Conventions

### Variables and Functions
- Use **snake_case** for variables and functions
- Use descriptive names that explain purpose

```python
# Good
user_count = 10
def calculate_monthly_revenue():
    pass

# Bad
uc = 10
def calcMR():
    pass
```

### Classes
- Use **PascalCase** for class names
- Use nouns that describe what the class represents

```python
# Good
class UserProfile:
    pass

class DatabaseConnection:
    pass

# Bad
class userprofile:
    pass

class db_conn:
    pass
```

### Constants
- Use **UPPER_SNAKE_CASE** for constants
- Define at module level

```python
# Good
MAX_RETRY_ATTEMPTS = 3
DEFAULT_TIMEOUT = 30

# Bad
max_retry_attempts = 3
defaultTimeout = 30
```

### Private Members
- Use **single leading underscore** for internal use
- Use **double leading underscore** for name mangling (rare)

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance
        self._account_id = self._generate_id()
        self.__secret_key = self._generate_secret()
    
    def _generate_id(self):
        """Internal method for ID generation."""
        pass
```

## Comments & Documentation

### Docstrings
- Use **triple quotes** for all docstrings
- Follow **Google style** format

```python
def calculate_compound_interest(principal, rate, time, compound_frequency=12):
    """Calculate compound interest.
    
    Args:
        principal (float): Initial amount of money.
        rate (float): Annual interest rate as decimal (e.g., 0.05 for 5%).
        time (float): Time period in years.
        compound_frequency (int, optional): Number of times interest compounds per year. 
            Defaults to 12.
    
    Returns:
        float: Final amount after compound interest.
    
    Raises:
        ValueError: If any input values are negative.
    
    Example:
        >>> calculate_compound_interest(1000, 0.05, 2)
        1104.89
    """
    if any(val < 0 for val in [principal, rate, time, compound_frequency]):
        raise ValueError("All values must be non-negative")
    
    return principal * (1 + rate / compound_frequency) ** (compound_frequency * time)
```

### Inline Comments
- Use sparingly to explain **why**, not **what**
- Keep comments up-to-date with code changes

```python
# Good
tax_rate = 0.08  # State tax rate for California

# Process only active users to avoid sending emails to deactivated accounts
active_users = [user for user in users if user.is_active]

# Bad
i += 1  # Increment i
```

## Functions & Classes

### Function Design
- Keep functions small and focused on single responsibility
- Use type hints for better code documentation

```python
from typing import List, Optional, Dict

def filter_active_users(users: List[Dict[str, any]]) -> List[Dict[str, any]]:
    """Filter list to return only active users."""
    return [user for user in users if user.get('is_active', False)]

def get_user_by_id(user_id: int, users: List[Dict[str, any]]) -> Optional[Dict[str, any]]:
    """Find user by ID, return None if not found."""
    for user in users:
        if user.get('id') == user_id:
            return user
    return None
```

### Class Design
- Use `__init__` for initialization
- Implement `__str__` and `__repr__` for debugging
- Use properties for computed attributes

```python
class Rectangle:
    """Represents a rectangle with width and height."""
    
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height
    
    @property
    def area(self) -> float:
        """Calculate rectangle area."""
        return self.width * self.height
    
    @property
    def perimeter(self) -> float:
        """Calculate rectangle perimeter."""
        return 2 * (self.width + self.height)
    
    def __str__(self) -> str:
        return f"Rectangle({self.width}x{self.height})"
    
    def __repr__(self) -> str:
        return f"Rectangle(width={self.width}, height={self.height})"
```

## Imports

### Import Order
1. Standard library imports
2. Third-party imports
3. Local application imports

```python
# Standard library
import os
import sys
from datetime import datetime
from typing import List, Dict

# Third-party
import requests
import pandas as pd
from flask import Flask, request

# Local imports
from .models import User
from .utils import validate_email
```

### Import Style
- Use absolute imports when possible
- Avoid wildcard imports (`from module import *`)

```python
# Good
from mypackage.utils import helper_function
import mypackage.constants as const

# Bad
from mypackage.utils import *
```

## Error Handling

### Exception Handling
- Catch specific exceptions, not bare `except:`
- Use `finally` for cleanup code
- Create custom exceptions for domain-specific errors

```python
# Good
class ValidationError(Exception):
    """Raised when data validation fails."""
    pass

def process_user_data(data):
    try:
        validated_data = validate_data(data)
        result = save_to_database(validated_data)
    except ValidationError as e:
        logger.error(f"Validation failed: {e}")
        raise
    except DatabaseError as e:
        logger.error(f"Database error: {e}")
        # Handle gracefully or re-raise
        raise
    finally:
        cleanup_resources()
    
    return result

# Bad
def process_user_data(data):
    try:
        result = save_to_database(data)
    except:
        pass  # Silent failure - very bad!
```

## Best Practices

### General Guidelines

1. **Use list comprehensions** for simple transformations
```python
# Good
squares = [x**2 for x in range(10)]
active_users = [user for user in users if user.is_active]

# Bad
squares = []
for x in range(10):
    squares.append(x**2)
```

2. **Use f-strings** for string formatting
```python
# Good
message = f"Hello {name}, you have {count} new messages"

# Acceptable
message = "Hello {}, you have {} new messages".format(name, count)

# Bad
message = "Hello " + name + ", you have " + str(count) + " new messages"
```

3. **Use context managers** for resource management
```python
# Good
with open('file.txt', 'r') as f:
    content = f.read()

# Good
with database.connection() as conn:
    result = conn.execute(query)
```

4. **Use enumerate() and zip()** appropriately
```python
# Good
for i, item in enumerate(items):
    print(f"{i}: {item}")

for name, age in zip(names, ages):
    print(f"{name} is {age} years old")

# Bad
for i in range(len(items)):
    print(f"{i}: {items[i]}")
```

### Performance Tips

- Use `set()` for membership testing on large collections
- Use `dict.get()` instead of checking `if key in dict`
- Consider using `collections.defaultdict` for grouping operations

```python
from collections import defaultdict

# Good - O(1) lookup
valid_ids = {1, 2, 3, 4, 5}
if user_id in valid_ids:
    process_user()

# Good - safe dictionary access
user_name = user_data.get('name', 'Unknown')

# Good - grouping with defaultdict
groups = defaultdict(list)
for item in items:
    groups[item.category].append(item)
```

## Tools & Automation

### Recommended Tools
- **Black**: Code formatter
- **isort**: Import sorting
- **flake8**: Linting
- **mypy**: Type checking
- **pytest**: Testing

### Pre-commit Configuration
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/psf/black
    rev: 22.3.0
    hooks:
      - id: black
  - repo: https://github.com/pycqa/isort
    rev: 5.10.1
    hooks:
      - id: isort
  - repo: https://github.com/pycqa/flake8
    rev: 4.0.1
    hooks:
      - id: flake8
```

---

*This style guide is based on PEP 8 and industry best practices. Consistency within your codebase is more important than strict adherence to any single standard.*
