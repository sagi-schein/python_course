# Functions and the Standard Library

> **Module:** Basic Python · **Lecture 2 of 4**

## Learning objectives

- Define and call functions with positional, keyword, and default arguments
- Understand scope, return values, and `*args` / `**kwargs`
- Write docstrings and use type hints
- Work with lambdas and higher-order functions
- Import and use modules from the standard library

---

## 1. Why functions?

A function is a piece of code that packages reusable logic behind a callable name
Advantages 
1. Gives meaning to blocks of code
2. Reduce repetition
3. Make the code readble 
4. Make the code testable

A function in python is defined by its name, parameters, and return value

```python
def add(x,y):
    return x+y

count = add(1,2)
print(count)        # 3
```

- `def` starts a definition
- `x`,`y` are the **parameter**
- `return` sends a value back to the caller

Another example a function with a string parameter 

```python
def greet(name):
    return f"Hello, {name}!"

message = greet("Ada")
print(message)        # Hello, Ada!
```


- `def` starts a definition
- `name` is a **parameter**
- `"Ada"` is an **argument**
- `return` sends a value back to the caller

A function with no `return` returns `None`


A question : 
look at the function below try to guess what will print

```python
def add(x,y):
    x+y

print(add(1,2))      # ?  
```


---

## 2. Arguments

### Positional and keyword

```python
def power(base, exponent):
    return base ** exponent

power(2, 3)                      # 8   positional
power(base=2, exponent=3)        # 8   keyword
power(exponent=3, base=2)        # 8   order-independent when keyed
```

### Default values

```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Ada")                 # Hello, Ada!
greet("Ada", "Hi")           # Hi, Ada!
```

> ⚠️ Never use a mutable default (`def f(items=[])`). It is shared across calls. Use `None`:
> ```python
> def f(items=None):
>     if items is None:
>         items = []
> ```

### Variable-length: `*args` and `**kwargs`

```python
def total(*numbers):           # tuple of positional args
    return sum(numbers)

total(1, 2, 3, 4)              # 10

def describe(**attrs):         # dict of keyword args
    for key, value in attrs.items():
        print(f"{key}: {value}")

describe(color="red", size="L")
```


Python calling convention vs C calling convention -> TBD


---

## 3. Scope

Variables defined inside a function are **local** and invisible outside it.

```python
def f():
    x = 10        # local
    return x

f()
# print(x)  -> NameError
```

Python resolves names using the **LEGB** rule: Local → Enclosing → Global → Built-in. Use `global` / `nonlocal` only when you truly must (rarely).

---

## 4. Docstrings and type hints

```python
def area(width: float, height: float) -> float:
    """Return the area of a rectangle.

    Args:
        width: the rectangle's width.
        height: the rectangle's height.

    Returns:
        The computed area.
    """
    return width * height
```

Type hints are optional and not enforced at runtime, but they document intent and power tools like `mypy` and IDE autocomplete.

---

## 5. Lambdas and higher-order functions

A **lambda** is a small anonymous function:

```python
square = lambda x: x ** 2
square(5)        # 25
```

Functions are first-class values — you can pass them around:

```python
nums = [1, 2, 3, 4, 5]

list(map(lambda x: x * 2, nums))        # [2, 4, 6, 8, 10]
list(filter(lambda x: x % 2 == 0, nums)) # [2, 4]
sorted(nums, key=lambda x: -x)          # [5, 4, 3, 2, 1]
```

Often a **comprehension** is clearer than `map`/`filter`:

```python
[x * 2 for x in nums]              # [2, 4, 6, 8, 10]
[x for x in nums if x % 2 == 0]    # [2, 4]
```

---

## 6. The standard library — "batteries included"

You import modules with `import`:

```python
import math
math.sqrt(16)        # 4.0
math.pi              # 3.14159...

from random import randint, choice
randint(1, 6)        # dice roll
choice(["a", "b", "c"])

import datetime as dt
dt.date.today()      # 2026-06-19
```

### A tour of useful modules

| Module | Use |
|--------|-----|
| `math` | Math functions and constants |
| `random` | Random numbers and choices |
| `datetime` | Dates and times |
| `os` / `pathlib` | Files and paths |
| `json` | Read/write JSON |
| `collections` | `Counter`, `defaultdict`, `deque` |
| `itertools` | Iterator building blocks |
| `re` | Regular expressions |
| `statistics` | Mean, median, stdev |

### Examples

```python
from collections import Counter
Counter("mississippi")     # Counter({'s': 4, 'i': 4, 'p': 2, 'm': 1})

import json
data = json.dumps({"name": "Ada", "age": 36})   # to string
obj = json.loads(data)                            # back to dict

from pathlib import Path
Path("notes.txt").write_text("hello")
Path("notes.txt").read_text()
```

---

## 7. Import styles

```python
import math                 # math.sqrt(...)
from math import sqrt       # sqrt(...)
from math import sqrt as s  # s(...)
import numpy as np          # common alias convention
```

Avoid `from module import *` — it pollutes your namespace.

---

## Summary

- Functions package reusable logic; use `def`, parameters, and `return`.
- Support positional, keyword, default, `*args`, and `**kwargs`.
- Respect scope (LEGB); avoid mutable defaults.
- Document with docstrings and type hints.
- The standard library ships powerful modules — reach for them before writing your own.

## Exercises

1. Write `is_prime(n)` returning `True`/`False`. Use it to print all primes under 50.
2. Write `stats(*numbers)` returning a dict with the min, max, and mean.
3. Use `collections.Counter` to find the 3 most common words in a paragraph.
4. Write a function that takes a filename and returns the number of lines, using `pathlib`.

## Further reading

- [Python Standard Library reference](https://docs.python.org/3/library/)
- *Fluent Python*, ch. 7 (functions as objects)
