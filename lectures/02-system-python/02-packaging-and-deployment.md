# Packaging and Deployment

> **Module:** System Python · **Lecture 2 of 5**

## Learning objectives

- Understand modules, packages, and imports
- Manage dependencies with `pip` and virtual environments
- Structure and build a distributable package with `pyproject.toml`
- Publish to PyPI
- Containerize and deploy a Python application

---

## 1. Modules and packages

- A **module** is a single `.py` file.
- A **package** is a directory of modules (historically with an `__init__.py`).

```
mypackage/
├── __init__.py
├── core.py
└── utils.py
```

```python
from mypackage.core import main
from mypackage import utils
```

Absolute imports (from the package root) are preferred over fragile relative ones.

---

## 2. Dependency management

### pip and requirements

```
$ pip install requests
$ pip install "django>=4.2,<5.0"     # version constraints
$ pip freeze > requirements.txt       # snapshot
$ pip install -r requirements.txt     # reproduce
```

### Always use a virtual environment

```
$ python3 -m venv .venv
$ source .venv/bin/activate
```

Modern alternatives bundle env + dependency resolution: **`uv`**, **Poetry**, **PDM**, **pipenv**. `uv` is fast and increasingly popular:

```
$ uv venv
$ uv pip install requests
```

---

## 3. Structuring a project

A modern layout:

```
myproject/
├── pyproject.toml          # build config & metadata
├── README.md
├── LICENSE
├── src/
│   └── mypackage/
│       ├── __init__.py
│       └── core.py
└── tests/
    └── test_core.py
```

The `src/` layout prevents accidentally importing your package from the working directory instead of the installed copy.

---

## 4. `pyproject.toml`

The single, standardized config file (PEP 518/621):

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "mypackage"
version = "0.1.0"
description = "A small example package"
readme = "README.md"
requires-python = ">=3.9"
dependencies = [
    "requests>=2.28",
]
authors = [{ name = "Ada Lovelace", email = "ada@example.com" }]

[project.scripts]
mytool = "mypackage.core:main"      # creates a CLI command
```

### Building

```
$ pip install build
$ python -m build
# produces dist/mypackage-0.1.0.tar.gz and a .whl
```

- A **wheel** (`.whl`) is a pre-built, fast-to-install binary distribution.
- An **sdist** (`.tar.gz`) is the source distribution.

---

## 5. Installing your own package

```
$ pip install -e .        # editable / "develop" install
```

An editable install links to your source, so code changes take effect without reinstalling — ideal during development.

---

## 6. Publishing to PyPI

```
$ pip install twine
$ python -m build
$ twine upload dist/*               # to PyPI
$ twine upload -r testpypi dist/*   # to TestPyPI first!
```

Always test on **TestPyPI** before the real index. Use an API token, not your password.

---

## 7. Versioning

Follow **Semantic Versioning** — `MAJOR.MINOR.PATCH`:

- **MAJOR** — breaking changes
- **MINOR** — new, backward-compatible features
- **PATCH** — backward-compatible bug fixes

---

## 8. Deployment with Docker

A reproducible way to ship Python apps:

```dockerfile
# Dockerfile
FROM python:3.12-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["python", "-m", "mypackage"]
```

```
$ docker build -t myapp .
$ docker run --rm myapp
```

### Deployment checklist

- Pin dependencies (`requirements.txt` or a lockfile).
- Keep secrets in environment variables, not the image.
- Use a slim base image; add a `.dockerignore`.
- Run as a non-root user in production.
- Add a health check and structured logging.

---

## 9. Common deployment targets

| Target | Use case |
|--------|----------|
| Docker + VM / Kubernetes | General services |
| PaaS (Fly.io, Render, Heroku) | Quick web apps |
| Serverless (AWS Lambda) | Event-driven, bursty workloads |
| `pipx` | Distributing CLI tools to users |

---

## Summary

- Modules are files; packages are directories of modules.
- Use virtual environments and pin dependencies for reproducibility.
- `pyproject.toml` is the standard project/build config.
- Build wheels + sdists with `python -m build`; publish with `twine` (test on TestPyPI first).
- Follow SemVer.
- Docker gives reproducible deployments — keep images slim, secrets external, and processes non-root.

## Exercises

1. Convert one of your scripts into a proper package with a `src/` layout and `pyproject.toml`.
2. Add a `[project.scripts]` entry so your tool installs as a command; verify with `pip install -e .`.
3. Build a wheel and inspect its contents (`unzip -l dist/*.whl`).
4. Write a `Dockerfile` for your package and run it in a container.
5. Publish a trivial package to **TestPyPI** and install it into a fresh venv.

## Further reading

- [Python Packaging User Guide](https://packaging.python.org/)
- [PEP 621 — project metadata](https://peps.python.org/pep-0621/)
- [Docker docs — Python guide](https://docs.docker.com/language/python/)
