# Web Programming

> **Module:** System Python · **Lecture 5 of 5**

## Learning objectives

- Understand how Python web applications work (WSGI/ASGI)
- Build a small web service with Flask
- Build a typed, async API with FastAPI
- Handle routing, requests, responses, and JSON
- Connect to a database and render templates
- Know basic deployment and security practices

---

## 1. How Python serves the web

A browser sends an **HTTP request**; your application returns an **HTTP response**. Between the web server (nginx, Gunicorn, Uvicorn) and your code sits a standard interface:

- **WSGI** — the classic synchronous interface (Flask, Django).
- **ASGI** — the modern async interface (FastAPI, Starlette).

You rarely write to these directly — a framework does it for you.

---

## 2. Flask — a minimal app

Flask is a lightweight, beginner-friendly "micro-framework".

```python
# app.py
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, web!"

@app.route("/greet/<name>")
def greet(name):
    return f"Hello, {name}!"

@app.route("/api/sum", methods=["POST"])
def add():
    data = request.get_json()
    return jsonify(result=data["a"] + data["b"])

if __name__ == "__main__":
    app.run(debug=True)
```

```
$ pip install flask
$ python app.py        # serves on http://127.0.0.1:5000
```

- `@app.route` maps a **URL** to a function (a "view").
- `<name>` is a dynamic path parameter.
- `request` holds incoming data; `jsonify` returns JSON.

---

## 3. FastAPI — modern, typed, async

FastAPI uses type hints for validation and auto-generates interactive docs.

```python
# main.py
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float

@app.get("/")
async def home():
    return {"message": "Hello"}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}

@app.post("/items")
async def create_item(item: Item):
    return {"created": item.name, "price": item.price}
```

```
$ pip install "fastapi[standard]"
$ fastapi dev main.py     # docs at /docs
```

Benefits over raw Flask:

- Automatic request validation from type hints (via **Pydantic**).
- Interactive OpenAPI docs at `/docs` for free.
- Native `async` support for high concurrency.

---

## 4. Requests and responses

```python
# Query params:    /search?q=python&page=2
# Path params:     /users/42
# Body (JSON):     POST with a JSON payload
# Headers:         Authorization, Content-Type
# Status codes:    200, 201, 400, 404, 500
```

Flask example reading several inputs:

```python
@app.route("/search")
def search():
    q = request.args.get("q", "")          # query string
    page = int(request.args.get("page", 1))
    return jsonify(query=q, page=page)
```

---

## 5. Templates (server-rendered HTML)

Flask uses the **Jinja2** templating engine:

```python
from flask import render_template

@app.route("/profile/<name>")
def profile(name):
    return render_template("profile.html", name=name, skills=["python"])
```

```html
<!-- templates/profile.html -->
<h1>{{ name }}</h1>
<ul>
  {% for skill in skills %}
    <li>{{ skill }}</li>
  {% endfor %}
</ul>
```

> Jinja2 auto-escapes variables, protecting against cross-site scripting (XSS).

---

## 6. Talking to a database

```python
import sqlite3

conn = sqlite3.connect("app.db")
conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
conn.execute("INSERT INTO users (name) VALUES (?)", ("Ada",))   # parameterized!
conn.commit()

rows = conn.execute("SELECT * FROM users").fetchall()
```

> ⚠️ Always use **parameterized queries** (`?` placeholders). Never build SQL with string formatting — that opens you to SQL injection.

For larger apps, an **ORM** like SQLAlchemy maps Python objects to tables.

---

## 7. Deployment

Development servers (`app.run`, `fastapi dev`) are **not** for production. Use a real server:

```
# Flask (WSGI)
$ gunicorn app:app --workers 4

# FastAPI (ASGI)
$ uvicorn main:app --workers 4
```

Typical production stack: **nginx** (reverse proxy / TLS) → **Gunicorn/Uvicorn** (app server) → **your app**, all in a Docker container.

---

## 8. Security checklist

- Validate and sanitize all input (Pydantic helps).
- Use parameterized SQL queries.
- Serve over HTTPS; set secure cookies.
- Keep secrets in environment variables.
- Add authentication/authorization for protected routes.
- Rate-limit public endpoints.
- Keep dependencies updated.

---

## Summary

- Web apps speak HTTP; frameworks sit on WSGI (sync) or ASGI (async).
- Flask is a simple micro-framework; FastAPI adds type-based validation, async, and auto docs.
- Handle routing, query/path params, JSON bodies, and status codes.
- Render HTML with Jinja2 (auto-escaped); query databases with parameterized SQL.
- Deploy behind Gunicorn/Uvicorn + nginx, not the dev server.
- Security is non-negotiable: validate input, parameterize SQL, use HTTPS, protect secrets.

## Exercises

1. Build a Flask app with a `/` page and a `/greet/<name>` route.
2. Add a `POST /api/calc` endpoint that accepts `{"a":..., "b":..., "op":"+"}` and returns the result.
3. Rebuild exercise 2 in FastAPI with a Pydantic model and explore the `/docs` page.
4. Create a `todos` SQLite table and endpoints to add and list todos (use parameterized queries).
5. Add a Jinja2 template that renders the list of todos as HTML.

## Further reading

- [Flask documentation](https://flask.palletsprojects.com/)
- [FastAPI documentation](https://fastapi.tiangolo.com/)
- [OWASP Top Ten](https://owasp.org/www-project-top-ten/)
