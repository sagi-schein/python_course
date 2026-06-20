# Data Structures

> **Module:** Basic Python · **Lecture 3 of 4**

## Learning objectives

- Use Python's four core collections: list, tuple, dict, set
- Understand mutability and when to choose each structure
- Master indexing, slicing, and iteration
- Write comprehensions for lists, dicts, and sets
- Recognize common performance trade-offs

---

## 1. The four core collections

| Type | Ordered | Mutable | Duplicates | Syntax |
|------|---------|---------|------------|--------|
| `list` | ✅ | ✅ | ✅ | `[1, 2, 3]` |
| `tuple` | ✅ | ❌ | ✅ | `(1, 2, 3)` |
| `dict` | ✅* | ✅ | keys unique | `{"a": 1}` |
| `set` | ❌ | ✅ | ❌ | `{1, 2, 3}` |

\*Dicts preserve insertion order since Python 3.7.

---

## 2. Lists

An ordered, mutable sequence.

```python
fruits = ["apple", "banana", "cherry"]

fruits[0]            # "apple"      indexing
fruits[-1]           # "cherry"     negative index
fruits[1:3]          # ["banana", "cherry"]  slice
len(fruits)          # 3

fruits.append("date")        # add to end
fruits.insert(0, "kiwi")     # insert at index
fruits.remove("banana")      # remove by value
popped = fruits.pop()        # remove & return last
fruits.sort()                # sort in place
fruits.reverse()
"apple" in fruits            # membership test -> bool
```

### Slicing patterns

```python
nums = [0, 1, 2, 3, 4, 5]
nums[2:]        # [2, 3, 4, 5]
nums[:3]        # [0, 1, 2]
nums[::2]       # [0, 2, 4]   every other
nums[::-1]      # [5, 4, 3, 2, 1, 0]  reversed
```

### Copying — beware aliasing

```python
a = [1, 2, 3]
b = a            # b is the SAME list
b.append(4)      # a is now [1, 2, 3, 4] too!

c = a.copy()     # independent shallow copy
c = a[:]         # also a copy
```

---

## 3. Tuples

Like lists but **immutable**. Use for fixed collections and as dict keys.

```python
point = (3, 4)
x, y = point          # unpacking
point[0]              # 3
# point[0] = 5        -> TypeError

# Common in returns:
def minmax(nums):
    return min(nums), max(nums)   # returns a tuple

lo, hi = minmax([4, 1, 8])
```

---

## 4. Dictionaries

Key → value mappings. The workhorse of Python.

```python
person = {"name": "Ada", "age": 36}

person["name"]              # "Ada"
person.get("email")         # None (no KeyError)
person.get("email", "n/a")  # "n/a" default

person["email"] = "ada@x.com"   # add / update
del person["age"]               # remove

"name" in person                # True
person.keys()                   # dict_keys([...])
person.values()
person.items()                  # key-value pairs

for key, value in person.items():
    print(key, value)
```

### Useful patterns

```python
from collections import defaultdict
counts = defaultdict(int)
for ch in "hello":
    counts[ch] += 1        # no KeyError on first hit

# Merge dicts (3.9+)
merged = {"a": 1} | {"b": 2}
```

---

## 5. Sets

Unordered collections of **unique** items. Fast membership testing.

```python
s = {1, 2, 3, 3, 2}     # {1, 2, 3} — dupes removed

s.add(4)
s.discard(1)
3 in s                   # True, O(1) average

a = {1, 2, 3}
b = {2, 3, 4}
a | b      # union          {1, 2, 3, 4}
a & b      # intersection   {2, 3}
a - b      # difference     {1}
a ^ b      # symmetric diff {1, 4}
```

Dedupe a list quickly:

```python
unique = list(set([1, 1, 2, 3, 3]))   # order not preserved
```

---

## 6. Comprehensions

Concise construction from an iterable.

```python
# list
squares = [x**2 for x in range(5)]            # [0, 1, 4, 9, 16]
evens   = [x for x in range(10) if x % 2 == 0]

# dict
sq_map = {x: x**2 for x in range(5)}          # {0:0, 1:1, ...}

# set
letters = {ch for ch in "hello"}              # {'h','e','l','o'}

# nested
grid = [(r, c) for r in range(2) for c in range(2)]
```

Comprehensions are usually faster and clearer than equivalent loops — but don't nest more than two levels; reach for a regular loop when it gets dense.

---

## 7. Choosing a structure

- **Order matters, will change** → `list`
- **Fixed record, hashable** → `tuple`
- **Lookup by key** → `dict`
- **Uniqueness / set math / fast membership** → `set`

### Performance cheat-sheet

| Operation | list | dict / set |
|-----------|------|------------|
| Membership (`x in c`) | O(n) | O(1) avg |
| Index access | O(1) | n/a |
| Append | O(1) | O(1) |
| Insert/delete middle | O(n) | n/a |

---

## Summary

- Lists are ordered and mutable; tuples are ordered and immutable.
- Dicts map keys to values; sets store unique items with fast membership.
- Watch out for aliasing — assignment shares references; use `.copy()`.
- Comprehensions build collections concisely.
- Pick the structure that matches your access pattern for clarity and speed.

## Exercises

1. Given a list of numbers, return a new list with duplicates removed, order preserved.
2. Count word frequencies in a sentence using a dict.
3. Given two lists, return the items common to both, then the items in either but not both.
4. Build a dict mapping each word in a sentence to its length, using a comprehension.
5. Invert a dict (swap keys and values). What happens with duplicate values?

## Further reading

- [Data Structures — Python Tutorial](https://docs.python.org/3/tutorial/datastructures.html)
- *Fluent Python*, ch. 2–3
