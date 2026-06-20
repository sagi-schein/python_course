# Networking

> **Module:** System Python · **Lecture 4 of 5**

## Learning objectives

- Understand the networking stack and client/server model
- Use low-level `socket` programming for TCP and UDP
- Make HTTP requests with `urllib` and `requests`
- Work with JSON APIs
- Appreciate async networking and basic security

---

## 1. The networking model

Networking is layered. For application programmers the key ideas are:

- **IP address** — identifies a host (e.g. `93.184.216.34`).
- **Port** — identifies a service on that host (e.g. `80` HTTP, `443` HTTPS, `22` SSH).
- **TCP** — reliable, ordered, connection-based stream. Used by HTTP, SSH, email.
- **UDP** — fast, connectionless, no delivery guarantee. Used by DNS, video, games.

Most programs follow a **client/server** model: a server listens on a port; clients connect to it.

---

## 2. Low-level sockets — TCP

### A minimal server

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind(("127.0.0.1", 9000))
    server.listen()
    print("Listening on :9000")
    conn, addr = server.accept()          # blocks until a client connects
    with conn:
        data = conn.recv(1024)            # up to 1024 bytes
        print("Received:", data.decode())
        conn.sendall(b"Hello from server")
```

### A minimal client

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as client:
    client.connect(("127.0.0.1", 9000))
    client.sendall(b"Hi server")
    print(client.recv(1024).decode())
```

- `AF_INET` = IPv4, `SOCK_STREAM` = TCP (`SOCK_DGRAM` = UDP).
- Data is **bytes** — `.encode()` to send strings, `.decode()` to read them.
- TCP is a stream: one `recv` may return partial data; loop until you have what you expect.

---

## 3. HTTP the easy way — `requests`

Almost all real-world networking is HTTP. The third-party `requests` library is the standard:

```python
import requests

r = requests.get("https://api.github.com/users/octocat")
print(r.status_code)        # 200
print(r.headers["Content-Type"])
data = r.json()             # parse JSON body to a dict
print(data["name"])

# POST with a JSON body
resp = requests.post(
    "https://httpbin.org/post",
    json={"name": "Ada"},
    headers={"Authorization": "Bearer TOKEN"},
    timeout=10,
)
resp.raise_for_status()     # raise on 4xx/5xx
```

### Status codes worth knowing

| Range | Meaning |
|-------|---------|
| 2xx | Success (200 OK, 201 Created) |
| 3xx | Redirect |
| 4xx | Client error (400, 401, 403, 404) |
| 5xx | Server error (500, 503) |

> Always set a `timeout` — without one a hung server can freeze your program indefinitely.

---

## 4. Standard library: `urllib`

If you can't add dependencies:

```python
from urllib.request import urlopen
import json

with urlopen("https://api.github.com/users/octocat") as resp:
    data = json.loads(resp.read().decode())
print(data["name"])
```

`requests` is friendlier, but `urllib` is always available.

---

## 5. Working with JSON APIs

JSON is the lingua franca of web APIs.

```python
import json

obj = {"name": "Ada", "skills": ["python", "math"]}
text = json.dumps(obj, indent=2)     # Python -> JSON string
back = json.loads(text)              # JSON string -> Python
```

A typical API workflow: build a URL with query params, send a request with auth headers, check the status code, parse `.json()`, handle errors.

```python
import requests

def get_user(username):
    r = requests.get(f"https://api.github.com/users/{username}", timeout=10)
    if r.status_code == 404:
        return None
    r.raise_for_status()
    return r.json()
```

---

## 6. Async networking (high concurrency)

For thousands of simultaneous connections, blocking calls don't scale. Use async clients:

```python
import asyncio, httpx

async def fetch(client, url):
    r = await client.get(url)
    return r.status_code

async def main(urls):
    async with httpx.AsyncClient() as client:
        tasks = [fetch(client, u) for u in urls]
        return await asyncio.gather(*tasks)

# asyncio.run(main(["https://example.com"] * 100))
```

---

## 7. Security essentials

- **Always use HTTPS** (`https://`) — `requests` verifies TLS certificates by default; don't disable it.
- **Never hard-code secrets** — read tokens from environment variables.
- **Validate and sanitize** all input from the network — treat it as hostile.
- **Set timeouts** and handle connection errors gracefully.
- Respect rate limits and `robots.txt` when scraping.

---

## Summary

- IP + port identify a service; TCP is reliable streams, UDP is fast and connectionless.
- `socket` gives low-level control; data is always bytes.
- For HTTP, use `requests` — handle status codes, set timeouts, call `.json()`.
- `urllib` is the dependency-free fallback.
- JSON is the standard data format; `json.dumps`/`loads` convert both ways.
- Use async clients (`httpx`/`aiohttp`) for high concurrency; always use HTTPS and keep secrets in the environment.

## Exercises

1. Build a TCP "echo" server and a client that sends a message and prints the reply.
2. Use `requests` to fetch a public API (e.g. GitHub user info) and print three fields.
3. Add robust error handling: timeouts, non-200 status codes, and JSON parse failures.
4. Write a function that POSTs JSON to `https://httpbin.org/post` and returns the echoed body.
5. Fetch 20 URLs sequentially with `requests`, then concurrently with `httpx` + `asyncio`. Compare timings.

## Further reading

- [`socket` docs](https://docs.python.org/3/library/socket.html)
- [`requests` documentation](https://requests.readthedocs.io/)
- [`httpx` documentation](https://www.python-httpx.org/)
