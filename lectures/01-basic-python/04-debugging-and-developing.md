# Debugging and Developing

> **Module:** Basic Python · **Lecture 4 of 4**

## Learning objectives

- Read and interpret tracebacks
- Handle errors with `try`/`except`/`finally`
- Debug with `print`, logging, and `pdb`
- Set up a clean development environment (venv, editor, linters)
- Write and run tests with `pytest`

---

## 1. Reading a traceback

When code raises an unhandled exception, Python prints a **traceback** — read it bottom-up:

```
Traceback (most recent call last):
  File "app.py", line 10, in <module>
    result = divide(10, 0)
  File "app.py", line 6, in divide
    return a / b
ZeroDivisionError: division by zero
```

- The **last line** is the exception type and message — start here.
- The frames above show the call chain that led there.
- The top frame is where execution began; the bottom is where it failed.

### Common exceptions

| Exception | Cause |
|-----------|-------|
| `SyntaxError` | Invalid code structure |
| `NameError` | Using an undefined variable |
| `TypeError` | Wrong type for an operation |
| `ValueError` | Right type, wrong value |
| `KeyError` / `IndexError` | Missing dict key / list index |
| `AttributeError` | Accessing a missing attribute |
| `ImportError` | Module not found |

---

## 2. Handling errors

```python
try:
    value = int(input("Enter a number: "))
    result = 10 / value
except ValueError:
    print("That wasn't a number.")
except ZeroDivisionError:
    print("Can't divide by zero.")
else:
    print(f"Result: {result}")     # runs if no exception
finally:
    print("Done.")                 # always runs
```

### Raising your own

```python
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError("Insufficient funds")
    return balance - amount
```

> **Principle:** catch only the exceptions you can handle. A bare `except:` hides bugs. Prefer `except SomeError:` and let unexpected errors surface.

---

## 3. Debugging techniques

### Print debugging

Quick and universal:

```python
print(f"DEBUG: x={x!r}, type={type(x)}")
```

The `!r` gives the `repr()` — useful to distinguish `"5"` from `5`.

### Logging — better than print for real programs

```python
import logging
logging.basicConfig(level=logging.DEBUG)

logging.debug("Detailed info for diagnosing")
logging.info("Confirmation things work")
logging.warning("Something unexpected")
logging.error("A serious problem")
```

Logging can be turned off, redirected to files, and leveled — `print` cannot.

### The interactive debugger `pdb`

```python
import pdb; pdb.set_trace()    # drop into debugger here
# or, Python 3.7+:
breakpoint()
```

Inside pdb:

| Command | Action |
|---------|--------|
| `n` | next line |
| `s` | step into function |
| `c` | continue |
| `p x` | print variable `x` |
| `l` | list source around current line |
| `q` | quit |

Most editors (VS Code, PyCharm) offer breakpoints with a click and a visual variable inspector — prefer these for nontrivial bugs.

---

## 4. Setting up a dev environment

### Virtual environments

Isolate each project's dependencies:

```
$ python3 -m venv .venv
$ source .venv/bin/activate      # Windows: .venv\Scripts\activate
(.venv) $ pip install requests
(.venv) $ pip freeze > requirements.txt
```

Recreate elsewhere with `pip install -r requirements.txt`.

### Tooling

| Tool | Purpose |
|------|---------|
| `black` | Auto-formatter (no debates) |
| `ruff` / `flake8` | Linting — catches bugs & style issues |
| `mypy` | Static type checking |
| `isort` | Sort imports |

```
$ pip install black ruff
$ black .          # format
$ ruff check .     # lint
```

---

## 5. Testing with pytest

Tests prove your code works and protect against regressions.

```python
# math_utils.py
def add(a, b):
    return a + b

# test_math_utils.py
from math_utils import add

def test_add_positive():
    assert add(2, 3) == 5

def test_add_negative():
    assert add(-1, -1) == -2

def test_add_raises_on_str():
    import pytest
    with pytest.raises(TypeError):
        add("a", 1)        # only if you validate types
```

Run them:

```
$ pip install pytest
$ pytest -v
```

### Good testing habits

- One behavior per test; name tests descriptively.
- Cover the happy path **and** edge cases (empty input, zero, negatives, large values).
- Write a failing test that reproduces a bug *before* fixing it.

---

## 6. A practical debugging workflow

1. **Reproduce** the bug reliably.
2. **Read** the traceback — exact line and exception.
3. **Isolate** — shrink to the smallest failing case.
4. **Inspect** state with logging or a debugger.
5. **Hypothesize, change one thing, re-test.**
6. **Add a test** so it never regresses.

---

## Summary

- Read tracebacks bottom-up; the last line names the failure.
- Use `try`/`except`/`else`/`finally` and catch narrowly.
- Debug with logging and `pdb`/`breakpoint()`, not just `print`.
- Use venvs, formatters, linters, and type checkers for clean code.
- Write `pytest` tests covering happy paths and edge cases.

## Exercises

1. Write a function that reads an integer from the user and re-prompts until valid input is given (use `try`/`except` in a loop).
2. Add logging to a small script at `DEBUG` and `INFO` levels; switch the level and observe the difference.
3. Set `breakpoint()` inside a buggy loop and step through to find an off-by-one error.
4. Write a `divide(a, b)` function with full test coverage, including the divide-by-zero case.
5. Create a venv, install `requests`, and freeze a `requirements.txt`.

## Further reading

- [Errors and Exceptions — Python Tutorial](https://docs.python.org/3/tutorial/errors.html)
- [pdb documentation](https://docs.python.org/3/library/pdb.html)
- [pytest documentation](https://docs.pytest.org/)
