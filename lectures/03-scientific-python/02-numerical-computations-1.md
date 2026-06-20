# Numerical Computations — Part 1: NumPy

> **Module:** Scientific Python · **Lecture 2 of 4**

## Learning objectives

- Understand why NumPy arrays beat Python lists for numerics
- Create, index, and reshape `ndarray`s
- Use vectorization and broadcasting instead of loops
- Apply aggregations and axis-based operations
- Generate random data and set seeds for reproducibility

---

## 1. Why NumPy?

Pure-Python lists are flexible but slow for math: each element is a boxed object and loops run in the interpreter. NumPy stores data in a **contiguous typed buffer** and runs operations in compiled C — often 10–100× faster — with a concise, expressive API.

```
$ pip install numpy
```

```python
import numpy as np
```

---

## 2. Creating arrays

```python
np.array([1, 2, 3])                 # from a list
np.zeros((2, 3))                    # 2x3 of zeros
np.ones(5)
np.full((2, 2), 7)
np.arange(0, 10, 2)                 # [0 2 4 6 8]
np.linspace(0, 1, 5)                # 5 evenly spaced in [0,1]
np.eye(3)                           # 3x3 identity
np.random.rand(3, 3)                # uniform random
```

### Key attributes

```python
a = np.array([[1, 2, 3], [4, 5, 6]])
a.shape      # (2, 3)
a.ndim       # 2
a.size       # 6
a.dtype      # dtype('int64')
```

---

## 3. Indexing and slicing

```python
a = np.array([10, 20, 30, 40, 50])
a[0]          # 10
a[-1]         # 50
a[1:4]        # [20 30 40]
a[::2]        # [10 30 50]

m = np.array([[1, 2, 3], [4, 5, 6]])
m[0, 1]       # 2   (row, col)
m[:, 1]       # [2 5]  whole column
m[1, :]       # [4 5 6]  whole row
```

### Boolean (mask) indexing — extremely powerful

```python
a = np.array([1, -2, 3, -4, 5])
a[a > 0]            # [1 3 5]
a[a < 0] = 0        # clamp negatives to 0 -> [1 0 3 0 5]
```

---

## 4. Vectorization — no loops

Operations apply element-wise across the whole array:

```python
a = np.array([1, 2, 3, 4])
a + 10           # [11 12 13 14]
a * 2            # [2 4 6 8]
a ** 2           # [1 4 9 16]
np.sqrt(a)       # [1. 1.41 1.73 2.]
a + a            # element-wise add
```

Compare loop vs. vectorized:

```python
# Slow
result = [x ** 2 for x in range(1_000_000)]
# Fast
result = np.arange(1_000_000) ** 2
```

---

## 5. Broadcasting

NumPy automatically "stretches" smaller arrays to match shapes during arithmetic:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])      # shape (2, 3)
row = np.array([10, 20, 30])   # shape (3,)

m + row          # row added to every row of m
# [[11 22 33]
#  [14 25 36]]

col = np.array([[100], [200]]) # shape (2, 1)
m + col          # col added to every column
```

Rule: dimensions are compatible when they are equal or one of them is 1.

---

## 6. Aggregations and axes

```python
a = np.array([[1, 2, 3],
              [4, 5, 6]])

a.sum()            # 21       all elements
a.mean()           # 3.5
a.max(), a.min()
a.std()

a.sum(axis=0)      # [5 7 9]    sum down columns
a.sum(axis=1)      # [6 15]     sum across rows
a.argmax()         # index of max
```

Think of `axis` as "the dimension that collapses."

---

## 7. Reshaping

```python
a = np.arange(12)
a.reshape(3, 4)        # 3x4
a.reshape(3, -1)       # -1 = "infer this dim"
a.T                    # transpose
a.flatten()            # back to 1-D
np.concatenate([a, a])
np.stack([a, a])
```

---

## 8. Random numbers and reproducibility

```python
rng = np.random.default_rng(seed=42)   # modern, recommended
rng.random(3)                # uniform [0,1)
rng.integers(1, 7, size=10)  # dice rolls
rng.normal(0, 1, size=5)     # standard normal
rng.choice([1, 2, 3], size=4)
```

Setting a **seed** makes results reproducible — essential for experiments and tests.

---

## 9. Performance tips

- Vectorize: replace Python loops with array operations.
- Avoid growing arrays in a loop; preallocate with `np.zeros`.
- Prefer in-place ops (`a += 1`) to reduce memory copies.
- Use the right `dtype` (`float32` vs `float64`) to control memory.
- Reach for `np.where`, `np.clip`, `np.cumsum`, etc., before writing loops.

---

## Summary

- NumPy arrays are typed, contiguous, and fast — use them for numerical work.
- Create with `array`, `zeros`, `arange`, `linspace`; inspect `shape`/`dtype`.
- Index with slices and boolean masks.
- Vectorize and let broadcasting handle mismatched shapes — avoid loops.
- Aggregate with `sum`/`mean`/`std` and the `axis` argument.
- Seed the random generator for reproducibility.

## Exercises

1. Create a 5×5 matrix of the numbers 1–25 and extract its second column and last row.
2. Given an array of temperatures, replace all values below freezing (0) with 0 using boolean indexing.
3. Without a loop, compute the element-wise distance from the origin for 1000 random (x, y) points.
4. Use broadcasting to normalize each column of a matrix to zero mean and unit variance.
5. Compare the runtime of squaring 10⁶ numbers with a list comprehension vs. NumPy (use `time`).

## Further reading

- [NumPy: the absolute basics](https://numpy.org/doc/stable/user/absolute_beginners.html)
- [NumPy broadcasting docs](https://numpy.org/doc/stable/user/basics.broadcasting.html)
