# System Administration

> **Module:** System Python · **Lecture 1 of 5**

## Learning objectives

- Interact with the operating system from Python (`os`, `sys`, `platform`)
- Work with files and paths using `pathlib`
- Run external commands with `subprocess`
- Read environment variables and command-line arguments
- Write robust automation scripts for everyday admin tasks

---

## 1. Python as a sysadmin tool

Python is a superb replacement for fragile shell scripts: it is cross-platform, readable, and has a rich standard library. Anything you'd do in Bash — moving files, calling tools, parsing output — you can do in Python with better error handling.

---

## 2. The `os` and `sys` modules

```python
import os, sys

os.getcwd()                  # current working directory
os.listdir(".")              # entries in a directory
os.makedirs("a/b", exist_ok=True)
os.rename("old.txt", "new.txt")
os.remove("temp.txt")
os.cpu_count()               # number of CPUs

sys.argv                     # command-line args list
sys.platform                 # 'linux', 'darwin', 'win32'
sys.exit(1)                  # exit with status code
```

### Platform info

```python
import platform
platform.system()            # 'Linux'
platform.python_version()    # '3.12.0'
```

---

## 3. Paths with `pathlib`

Prefer `pathlib` over string manipulation — it is object-oriented and cross-platform.

```python
from pathlib import Path

p = Path("/var/log") / "app.log"   # join with /
p.name           # 'app.log'
p.stem           # 'app'
p.suffix         # '.log'
p.parent         # PosixPath('/var/log')
p.exists()
p.is_file()
p.is_dir()

# Read / write
Path("notes.txt").write_text("hello")
content = Path("notes.txt").read_text()

# Iterate
for f in Path(".").glob("*.py"):
    print(f, f.stat().st_size)

# Recursive
for f in Path(".").rglob("*.log"):
    print(f)
```

---

## 4. Environment variables

```python
import os

os.environ.get("HOME")              # safe access
os.environ.get("API_KEY", "")       # with default
os.environ["DEBUG"] = "1"           # set (for child processes)
```

Keep secrets out of source code — read them from the environment or a `.env` file (via `python-dotenv`).

---

## 5. Command-line arguments

For quick scripts, `sys.argv`. For real tools, `argparse`:

```python
import argparse

parser = argparse.ArgumentParser(description="Backup a directory.")
parser.add_argument("source", help="directory to back up")
parser.add_argument("-o", "--output", default="backup.zip")
parser.add_argument("-v", "--verbose", action="store_true")
args = parser.parse_args()

if args.verbose:
    print(f"Backing up {args.source} -> {args.output}")
```

`argparse` gives you `--help`, validation, and type conversion for free.

---

## 6. Running external commands with `subprocess`

```python
import subprocess

# Simple: run and check for errors
subprocess.run(["ls", "-la"], check=True)

# Capture output
result = subprocess.run(
    ["git", "status", "--short"],
    capture_output=True, text=True, check=True
)
print(result.stdout)
print("exit code:", result.returncode)
```

> ⚠️ Avoid `shell=True` with untrusted input — it invites shell injection. Pass a list of arguments instead.

### Handling failures

```python
try:
    subprocess.run(["false"], check=True)
except subprocess.CalledProcessError as e:
    print(f"Command failed with code {e.returncode}")
```

---

## 7. Shutil — high-level file operations

```python
import shutil

shutil.copy("a.txt", "b.txt")        # copy file
shutil.copytree("src", "dst")        # copy directory tree
shutil.move("a.txt", "archive/")     # move
shutil.rmtree("old_dir")             # delete tree (careful!)
shutil.disk_usage("/")               # total/used/free
shutil.which("python3")              # find an executable in PATH
```

---

## 8. A practical example: log cleaner

```python
#!/usr/bin/env python3
"""Delete .log files older than N days."""
import argparse, time
from pathlib import Path

def clean(directory: Path, days: int, dry_run: bool):
    cutoff = time.time() - days * 86400
    for log in directory.rglob("*.log"):
        if log.stat().st_mtime < cutoff:
            print(f"{'Would delete' if dry_run else 'Deleting'}: {log}")
            if not dry_run:
                log.unlink()

if __name__ == "__main__":
    p = argparse.ArgumentParser()
    p.add_argument("dir", type=Path)
    p.add_argument("--days", type=int, default=30)
    p.add_argument("--dry-run", action="store_true")
    a = p.parse_args()
    clean(a.dir, a.days, a.dry_run)
```

Note the `if __name__ == "__main__":` guard — it lets the file be both a runnable script and an importable module.

---

## Summary

- `os`/`sys`/`platform` expose the operating system and interpreter.
- `pathlib` is the modern, cross-platform way to handle files and paths.
- Read configuration from environment variables; never hard-code secrets.
- Build CLIs with `argparse`; run external tools with `subprocess` (no `shell=True`).
- `shutil` offers high-level copy/move/delete operations.
- Always include a `__main__` guard and offer a `--dry-run` for destructive scripts.

## Exercises

1. Write a script that prints the total size of all files in a directory tree.
2. Build a CLI with `argparse` that renames all files in a folder to lowercase.
3. Use `subprocess` to call `git log --oneline` and count the commits.
4. Write a "disk space alert" that prints a warning if free space on `/` drops below 10%.
5. Extend the log cleaner above to also compress (not delete) old files using `shutil`/`gzip`.

## Further reading

- [`pathlib` docs](https://docs.python.org/3/library/pathlib.html)
- [`subprocess` docs](https://docs.python.org/3/library/subprocess.html)
- [`argparse` tutorial](https://docs.python.org/3/howto/argparse.html)
