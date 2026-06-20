# Numerical Computations — Part 2: SciPy, Pandas & Linear Algebra

> **Module:** Scientific Python · **Lecture 3 of 4**

## Learning objectives

- Manipulate tabular data with Pandas DataFrames
- Perform linear algebra with NumPy
- Use SciPy for optimization, integration, and statistics
- Fit models and interpolate data
- Combine the tools in a realistic analysis workflow

---

## 1. Pandas: labeled data

Where NumPy handles raw numeric arrays, **Pandas** adds labels, mixed types, and missing-data handling — the backbone of data analysis.

```python
import pandas as pd

df = pd.DataFrame({
    "name": ["Ada", "Alan", "Grace"],
    "age": [36, 41, 45],
    "score": [88.5, 92.0, 79.5],
})

df.head()
df.shape            # (3, 3)
df.dtypes
df.describe()       # summary statistics
```

### Selecting and filtering

```python
df["age"]                       # a column (Series)
df[["name", "age"]]             # multiple columns
df.loc[0]                       # row by label
df.iloc[0]                      # row by position
df[df["age"] > 40]              # boolean filter
df.sort_values("score", ascending=False)
```

### Transforming and aggregating

```python
df["passed"] = df["score"] >= 80          # new column
df.groupby("passed")["age"].mean()        # split-apply-combine
df["score"].fillna(df["score"].mean())    # handle missing data
```

### Reading and writing

```python
df = pd.read_csv("data.csv")
df.to_csv("out.csv", index=False)
df = pd.read_excel("data.xlsx")
df = pd.read_json("data.json")
```

---

## 2. Linear algebra with NumPy

```python
import numpy as np

A = np.array([[1, 2], [3, 4]])
b = np.array([5, 6])

A @ b                    # matrix-vector product
A @ A                    # matrix-matrix product
A.T                      # transpose
np.linalg.inv(A)         # inverse
np.linalg.det(A)         # determinant

# Solve Ax = b  (don't invert — solve directly)
x = np.linalg.solve(A, b)

# Eigenvalues / eigenvectors
vals, vecs = np.linalg.eig(A)
```

> Use `np.linalg.solve(A, b)` rather than `inv(A) @ b` — it is faster and numerically more stable.

---

## 3. SciPy: scientific algorithms

SciPy builds on NumPy with optimization, integration, interpolation, signal processing, and statistics.

```
$ pip install scipy
```

### Optimization — find a minimum

```python
from scipy.optimize import minimize

def f(x):
    return (x[0] - 3) ** 2 + (x[1] + 1) ** 2

result = minimize(f, x0=[0, 0])
print(result.x)         # ~[3, -1]
```

### Curve fitting

```python
from scipy.optimize import curve_fit
import numpy as np

def model(x, a, b):
    return a * np.exp(b * x)

xdata = np.linspace(0, 4, 50)
ydata = 2.5 * np.exp(0.8 * xdata) + np.random.normal(0, 1, 50)

params, _ = curve_fit(model, xdata, ydata)
print(params)           # estimated a, b
```

### Numerical integration

```python
from scipy import integrate
area, error = integrate.quad(lambda x: x ** 2, 0, 1)   # ~0.333
```

### Interpolation

```python
from scipy.interpolate import interp1d
f = interp1d([0, 1, 2], [0, 1, 4], kind="quadratic")
f(1.5)
```

---

## 4. Statistics with SciPy

```python
from scipy import stats

# Descriptive
stats.describe([2, 4, 4, 4, 5, 5, 7, 9])

# Hypothesis test: are two samples' means different?
a = np.random.normal(0, 1, 100)
b = np.random.normal(0.5, 1, 100)
t_stat, p_value = stats.ttest_ind(a, b)

# Linear regression
slope, intercept, r, p, se = stats.linregress([1, 2, 3], [2, 4, 6])
```

---

## 5. A realistic workflow

```python
import pandas as pd, numpy as np
from scipy import stats

# 1. Load
df = pd.read_csv("experiment.csv")

# 2. Clean
df = df.dropna(subset=["measurement"])
df = df[df["measurement"] > 0]

# 3. Explore
summary = df.groupby("group")["measurement"].agg(["mean", "std", "count"])

# 4. Test a hypothesis
group_a = df[df["group"] == "A"]["measurement"]
group_b = df[df["group"] == "B"]["measurement"]
t, p = stats.ttest_ind(group_a, group_b)

# 5. Report
print(summary)
print(f"t={t:.2f}, p={p:.4f}", "-> significant" if p < 0.05 else "-> not significant")
```

This **load → clean → explore → model → report** loop is the heart of scientific computing.

---

## Summary

- Pandas adds labels, mixed types, and missing-data handling to tabular work.
- Select with `loc`/`iloc`, filter with boolean masks, summarize with `groupby`.
- NumPy does linear algebra; prefer `solve` over explicit inverses.
- SciPy provides optimization, curve fitting, integration, interpolation, and statistics.
- A typical analysis follows load → clean → explore → model → report.

## Exercises

1. Load a CSV into a DataFrame, drop rows with missing values, and report the mean of each numeric column.
2. Group a dataset by a category column and compute the mean and count per group.
3. Solve the linear system `2x + y = 5`, `x − 3y = −1` using `np.linalg.solve`.
4. Fit `y = a·sin(bx)` to noisy data with `curve_fit` and plot the fit over the data.
5. Run a t-test between two groups and interpret the p-value at α = 0.05.

## Further reading

- [Pandas — 10 minutes to pandas](https://pandas.pydata.org/docs/user_guide/10min.html)
- [SciPy lecture notes](https://scipy-lectures.org/)
- [`numpy.linalg` docs](https://numpy.org/doc/stable/reference/routines.linalg.html)
