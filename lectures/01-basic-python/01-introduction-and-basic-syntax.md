# Introduction and Basic Syntax

> **Module:** Basic Python · **Lecture 1 of 4**

## Learning objectives

By the end of this lecture you will be able to:

- Explain what Python is and where it runs
- Install Python and use the interpreter (REPL) and scripts
- Work with variables, the core built-in types, and operators
- Read input, print output, and write your first programs
- Use comments, indentation, and basic control flow

---

## 1. What is Python?

Python is a high-level, interpreted, dynamically-typed programming language. "Interpreted" means code runs line-by-line through the Python interpreter rather than being compiled to machine code ahead of time. "Dynamically typed" means you do not declare the type of a variable — the interpreter figures it out at runtime.

Python emphasizes readability: blocks are defined by **indentation**, not braces. The guiding philosophy is captured in the *Zen of Python*:

```python
import this
```

### Why Python?

- Clean, readable syntax — close to pseudocode
- Huge standard library ("batteries included")
- Massive ecosystem (web, data science, automation, ML)
- Cross-platform and free

---

## 2. Running Python

There are three common ways to run code:

**1. The REPL** (Read–Eval–Print Loop) — interactive, great for experiments:

```
$ python3
>>> 2 + 2
4
>>> print("hello")
hello
```

**2. Scripts** — save code in a `.py` file and run it:

```
$ python3 hello.py
```

**3. Notebooks / IDEs** — Jupyter, VS Code, PyCharm.

Check your version:

```
$ python3 --version
Python 3.12.0
```

> ⚠️ Always use Python 3. Python 2 reached end-of-life in 2020.

---

## 3. Your first program

```python
# hello.py
print("Hello, world!")
```

`print()` is a built-in function that writes to standard output. Text in quotes is a **string**.

---

## 4. Variables and assignment

A variable is a name bound to a value. Assignment uses `=`:

```python
name = "Ada"
age = 36
pi = 3.14159
is_student = False
```

Names are case-sensitive, must start with a letter or underscore, and by convention use `snake_case`.

You can reassign freely, even to a different type:

```python
x = 10        # int
x = "ten"     # now a str — perfectly legal
```

---

## 5. Core built-in types

| Type | Example | Description |
|------|---------|-------------|
| `int` | `42`, `-7` | Whole numbers (arbitrary precision) |
| `float` | `3.14`, `2.0` | Floating-point numbers |
| `str` | `"hi"`, `'yo'` | Text |
| `bool` | `True`, `False` | Boolean |
| `NoneType` | `None` | "No value" |

Inspect a type with `type()`:

```python
type(42)        # <class 'int'>
type("hi")      # <class 'str'>
```

### Numbers

```python
7 + 3      # 10   addition
7 - 3      # 4    subtraction
7 * 3      # 21   multiplication
7 / 3      # 2.333...  true division (always float)
7 // 3     # 2    floor division
7 % 3      # 1    modulo (remainder)
7 ** 3     # 343  exponentiation
```

### Strings

```python
greeting = "Hello"
name = "Ada"

greeting + ", " + name      # "Hello, Ada"  concatenation
greeting * 3                # "HelloHelloHello"
len(greeting)               # 5
greeting.upper()            # "HELLO"
greeting[0]                 # "H"  indexing
greeting[1:4]               # "ell" slicing
```

**f-strings** are the modern way to build strings:

```python
name = "Ada"
age = 36
print(f"{name} is {age} years old.")   # Ada is 36 years old.
print(f"Next year she'll be {age + 1}.")
```

---

## 6. Input and output

```python
name = input("What's your name? ")   # input() always returns a str
print(f"Hi, {name}!")

age = int(input("How old are you? "))  # convert to int
print(f"In 10 years you'll be {age + 10}.")
```

---

## 7. Comments and indentation

```python
# This is a single-line comment

"""
This is a multi-line string,
often used as a docstring or block comment.
"""

if age >= 18:
    print("Adult")      # 4-space indent defines the block
    print("Can vote")
print("Done")           # back to outer level
```

Indentation is **syntactically significant**. Use 4 spaces. Never mix tabs and spaces.

---

## 8. Basic control flow (preview)

```python
temp = 30

if temp > 25:
    print("Hot")
elif temp > 15:
    print("Mild")
else:
    print("Cold")

for i in range(3):
    print(i)            # 0, 1, 2

n = 0
while n < 3:
    print(n)
    n += 1
```

We'll go deeper on functions and structures in the next lectures.

---

## Summary

- Python is interpreted, dynamically typed, and readability-focused.
- Run code via the REPL, scripts, or notebooks.
- Variables are names bound to values; types include `int`, `float`, `str`, `bool`, `None`.
- f-strings are the clean way to format text.
- Indentation defines blocks — use 4 spaces consistently.

## Exercises

1. Write a program that asks for two numbers and prints their sum, difference, product, and quotient.
2. Ask the user for their name and birth year, then print how old they'll turn this year.
3. Given a string, print its length, its uppercase form, and its first and last characters.
4. Write a program that converts a temperature in Celsius (entered by the user) to Fahrenheit. `F = C * 9/5 + 32`.

## Further reading

- [The Python Tutorial — Official Docs](https://docs.python.org/3/tutorial/)
- *Automate the Boring Stuff with Python*, ch. 1–2
