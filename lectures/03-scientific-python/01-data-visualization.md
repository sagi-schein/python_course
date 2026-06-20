# Data Visualization

> **Module:** Scientific Python · **Lecture 1 of 4**

## Learning objectives

- Understand the scientific Python ecosystem
- Create plots with Matplotlib's figure/axes model
- Choose the right chart for the data
- Use Seaborn and Pandas for fast statistical plots
- Apply principles of clear, honest visualization

---

## 1. The scientific Python stack

| Library | Role |
|---------|------|
| **NumPy** | N-dimensional arrays, fast math |
| **Pandas** | Labeled tabular data (DataFrames) |
| **Matplotlib** | Foundational plotting |
| **Seaborn** | Statistical plots on top of Matplotlib |
| **Plotly / Bokeh** | Interactive, web-based charts |

```
$ pip install numpy pandas matplotlib seaborn
```

---

## 2. Matplotlib basics

Matplotlib has two interfaces. Prefer the **object-oriented** one (figure + axes) for control:

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2 * np.pi, 100)
y = np.sin(x)

fig, ax = plt.subplots(figsize=(8, 4))
ax.plot(x, y, label="sin(x)", color="steelblue")
ax.set_title("A sine wave")
ax.set_xlabel("x")
ax.set_ylabel("amplitude")
ax.legend()
ax.grid(True, alpha=0.3)
fig.savefig("sine.png", dpi=150, bbox_inches="tight")
plt.show()
```

- A **Figure** is the whole canvas; an **Axes** is one plot within it.
- `subplots(rows, cols)` returns a grid of axes.

---

## 3. A gallery of chart types

```python
fig, axes = plt.subplots(2, 2, figsize=(10, 8))

# Line — trends over a continuous axis
axes[0, 0].plot(x, np.sin(x))

# Scatter — relationship between two variables
axes[0, 1].scatter(np.random.rand(50), np.random.rand(50), alpha=0.6)

# Bar — comparing categories
axes[1, 0].bar(["A", "B", "C"], [3, 7, 5])

# Histogram — distribution of one variable
axes[1, 1].hist(np.random.randn(1000), bins=30)

fig.tight_layout()
```

### Picking a chart

| Goal | Chart |
|------|-------|
| Trend over time | Line |
| Relationship between two variables | Scatter |
| Compare categories | Bar |
| Distribution of one variable | Histogram / box / violin |
| Part-to-whole | Stacked bar (avoid pie for many slices) |
| 2D density / matrix | Heatmap |

---

## 4. Multiple subplots and styling

```python
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4), sharey=True)
ax1.plot(x, np.sin(x)); ax1.set_title("sin")
ax2.plot(x, np.cos(x)); ax2.set_title("cos")

plt.style.use("seaborn-v0_8-whitegrid")   # built-in styles
```

---

## 5. Seaborn — statistical plots, fast

Seaborn works directly with Pandas DataFrames and produces attractive defaults:

```python
import seaborn as sns
import pandas as pd

tips = sns.load_dataset("tips")

sns.scatterplot(data=tips, x="total_bill", y="tip", hue="time")
sns.boxplot(data=tips, x="day", y="total_bill")
sns.heatmap(tips.corr(numeric_only=True), annot=True, cmap="coolwarm")
```

One line gives you grouping, color encoding, and a legend — work that is tedious in raw Matplotlib.

---

## 6. Plotting straight from Pandas

```python
import pandas as pd

df = pd.DataFrame({"year": [2020, 2021, 2022], "sales": [100, 140, 180]})
df.plot(x="year", y="sales", kind="bar")     # uses Matplotlib under the hood
```

---

## 7. Principles of good visualization

- **Tell one clear story per chart.** Remove clutter ("chartjunk").
- **Label everything** — title, axes, units, legend.
- **Start bar charts at zero** — truncated axes mislead.
- **Use color purposefully**, not decoratively; choose colorblind-safe palettes (e.g. `viridis`).
- **Match chart to data** — don't use a pie chart for a trend.
- **Show the data** — prefer scatter/strip plots over hiding points behind summary bars when the dataset is small.
- **Don't distort** — keep aspect ratios honest; avoid 3D effects.

---

## 8. Saving for reports vs. the web

```python
fig.savefig("figure.png", dpi=300, bbox_inches="tight")   # raster, for docs
fig.savefig("figure.svg")                                  # vector, scalable
```

For interactivity (zoom, hover tooltips, dashboards), use **Plotly**:

```python
import plotly.express as px
fig = px.scatter(tips, x="total_bill", y="tip", color="time")
fig.write_html("plot.html")
```

---

## Summary

- The stack: NumPy + Pandas for data, Matplotlib + Seaborn for plots.
- Use Matplotlib's figure/axes API for control; `subplots` builds grids.
- Match the chart type to the question (line, scatter, bar, histogram, heatmap).
- Seaborn and Pandas give fast statistical plots from DataFrames.
- Follow visualization principles: label clearly, don't distort, use color with intent.

## Exercises

1. Plot `y = x²` and `y = x³` on the same axes, with a legend, title, and labeled axes.
2. Generate 1000 normally-distributed points and draw a histogram with 40 bins.
3. Load Seaborn's `tips` dataset and make a box plot of `total_bill` by `day`.
4. Create a 2×2 grid showing a line, scatter, bar, and histogram in one figure.
5. Take a deliberately misleading chart (truncated y-axis) and fix it; explain what changed.

## Further reading

- [Matplotlib tutorials](https://matplotlib.org/stable/tutorials/index.html)
- [Seaborn tutorial](https://seaborn.pydata.org/tutorial.html)
- Edward Tufte, *The Visual Display of Quantitative Information*
