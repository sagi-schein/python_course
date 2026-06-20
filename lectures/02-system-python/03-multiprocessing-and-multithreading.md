# Multiprocessing and Multithreading

> **Module:** System Python · **Lecture 3 of 5**

## Learning objectives

- Distinguish concurrency from parallelism
- Understand the Global Interpreter Lock (GIL) and its consequences
- Use `threading` for I/O-bound work
- Use `multiprocessing` for CPU-bound work
- Use `concurrent.futures` for a unified, high-level API
- Get a working mental model of `asyncio`

---

## 1. Concurrency vs. parallelism

- **Concurrency** — dealing with many tasks at once by interleaving them (one cook juggling several dishes).
- **Parallelism** — actually doing many tasks at the same instant on multiple cores (several cooks).

The right tool depends on whether your workload is **I/O-bound** (waiting on network/disk) or **CPU-bound** (crunching numbers).

---

## 2. The GIL

CPython has a **Global Interpreter Lock**: only one thread executes Python bytecode at a time. Consequences:

- Threads do **not** speed up CPU-bound pure-Python code.
- Threads **do** help I/O-bound code, because the GIL is released while waiting on I/O.
- For CPU-bound parallelism, use **processes** (separate interpreters, separate GILs).

> Note: Python 3.13 introduces an experimental free-threaded build without the GIL, but the rules above remain the safe default mental model.

| Workload | Best tool |
|----------|-----------|
| I/O-bound (HTTP, disk, DB) | `threading` or `asyncio` |
| CPU-bound (math, parsing) | `multiprocessing` |

---

## 3. Threading (I/O-bound)

```python
import threading, time

def download(url):
    print(f"Start {url}")
    time.sleep(1)          # simulate network wait
    print(f"Done {url}")

urls = ["a", "b", "c"]
threads = [threading.Thread(target=download, args=(u,)) for u in urls]

for t in threads:
    t.start()
for t in threads:
    t.join()               # wait for all to finish
```

Three 1-second downloads finish in ~1 second, not 3 — the threads wait concurrently.

### Protecting shared state with locks

```python
lock = threading.Lock()
counter = 0

def increment():
    global counter
    with lock:             # only one thread in here at a time
        counter += 1
```

Without the lock, concurrent updates can interleave and corrupt the value (a **race condition**).

---

## 4. Multiprocessing (CPU-bound)

```python
from multiprocessing import Pool

def heavy(n):
    return sum(i * i for i in range(n))

if __name__ == "__main__":          # required guard on Windows/macOS
    with Pool(processes=4) as pool:
        results = pool.map(heavy, [10_000_00] * 4)
    print(results)
```

Each process has its own memory and interpreter, sidestepping the GIL. The cost: data must be **pickled** and copied between processes, and startup is heavier.

---

## 5. `concurrent.futures` — the unified API

The cleanest interface. Swap the executor to switch strategy:

```python
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

def work(x):
    return x * x

# I/O-bound -> threads
with ThreadPoolExecutor(max_workers=8) as ex:
    results = list(ex.map(work, range(10)))

# CPU-bound -> processes
with ProcessPoolExecutor() as ex:
    results = list(ex.map(work, range(10)))
```

### Handling results as they complete

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

with ThreadPoolExecutor() as ex:
    futures = {ex.submit(work, i): i for i in range(5)}
    for fut in as_completed(futures):
        print(fut.result())
```

---

## 6. asyncio (single-threaded concurrency)

`asyncio` runs many I/O-bound tasks cooperatively in one thread using `async`/`await`:

```python
import asyncio

async def fetch(name):
    print(f"Start {name}")
    await asyncio.sleep(1)        # non-blocking wait
    print(f"Done {name}")
    return name

async def main():
    results = await asyncio.gather(fetch("a"), fetch("b"), fetch("c"))
    print(results)

asyncio.run(main())
```

- `await` yields control while waiting, letting other coroutines run.
- Great for thousands of concurrent network connections (web servers, scrapers).
- You must use async-aware libraries (e.g. `aiohttp`, `httpx`) — a blocking call stalls the whole loop.

---

## 7. Choosing an approach

```
Is the work CPU-bound?
├── Yes → multiprocessing / ProcessPoolExecutor
└── No (I/O-bound)
    ├── Few tasks, simple code → ThreadPoolExecutor
    └── Many concurrent connections → asyncio
```

### Pitfalls

- Race conditions — guard shared state with locks.
- Deadlocks — acquiring locks in inconsistent orders.
- Over-parallelizing — context-switching and IPC overhead can exceed the gain.
- Forgetting the `if __name__ == "__main__":` guard with multiprocessing.

---

## Summary

- Concurrency interleaves tasks; parallelism runs them simultaneously.
- The GIL means threads don't parallelize CPU-bound Python — use processes for that.
- `threading`/`asyncio` shine for I/O-bound work; `multiprocessing` for CPU-bound.
- `concurrent.futures` gives one clean API for both pools.
- Protect shared state; beware race conditions, deadlocks, and overhead.

## Exercises

1. Download (simulate with `time.sleep`) 10 "URLs" sequentially, then with a `ThreadPoolExecutor`. Compare timings.
2. Compute the sum of squares up to 10⁷ four times, sequentially vs. with a `ProcessPoolExecutor`. Compare.
3. Demonstrate a race condition by incrementing a shared counter from many threads without a lock; then fix it.
4. Rewrite exercise 1 using `asyncio.gather` and `asyncio.sleep`.
5. Explain in your own words why threading does not speed up exercise 2.

## Further reading

- [`concurrent.futures` docs](https://docs.python.org/3/library/concurrent.futures.html)
- [`asyncio` docs](https://docs.python.org/3/library/asyncio.html)
- David Beazley, *Understanding the GIL* (talk)
