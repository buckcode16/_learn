Here’s a tight “80/20” Python guide — the features and syntax that cover ~80% of day-to-day dev work. Skim the bullets, copy the snippets.

Core concepts to master

Data & literals: int, float, bool, str, None, list [], tuple (), dict {}, set {}, range().

Assignment & unpacking: a, b = b, a; head, *mid, tail = seq.

Truthiness: empty containers/0/None → False.

Operators: arithmetic + - * / // % **; comparison == != <; chaining 0 < x < 10; membership in; identity is (use only for None).

Control flow: if/elif/else, for, while, break/continue, ternary x if cond else y.

Functions: def, keyword args, *args/**kwargs, keyword-only def f(*, x), return early, docstrings, type hints.

Collections API (workhorses):

list: append, extend, insert, pop, sort, slicing lst[a:b:c]

dict: get, items, keys, values, setdefault, update

set: | & - ^, fast membership tests


Iteration helpers: enumerate, zip, range, sorted(..., key=..., reverse=...).

Comprehensions: list/dict/set comps; generator expressions for streaming.

Strings: f-strings f"{name=}", .split()/.join()/.strip(), .replace(), .startswith(), .format() rarely.

Files & paths: pathlib.Path + with open(...) as f:.

Errors: try/except/else/finally, raise specific exceptions, EAFP (“try it, handle failure”) over LBYL.

Modules: import x as y, from x import y, if __name__ == "__main__":.

Classes (lightweight): methods, __init__, __repr__; prefer @dataclass for data carriers.

Typing (pragmatic): annotate public funcs; Optional, Iterable, Mapping, list[str].

Batteries: json, datetime, collections (Counter, deque, defaultdict), itertools, functools, pathlib.

Style & tooling: PEP 8, black (format), ruff (lint), pytest (tests).


Mini cookbook (copy/paste)

Collections, iteration, sorting

nums = [3, 1, 4, 1, 5]
squares = [n*n for n in nums if n % 2 == 1]

for i, n in enumerate(nums, start=1):
    print(i, n)

pairs = list(zip(["a", "b", "c"], [1, 2, 3]))

# Sorting with key (no cmp)
words = ["Pear", "apple", "Banana"]
print(sorted(words, key=str.lower))  # ['apple', 'Banana', 'Pear']

# Dict & set basics
counts = {"a": 1, "b": 2}
counts["c"] = counts.get("c", 0) + 1
unique = set(words)

Functions (with hints, defaults, keyword-only)

from collections.abc import Iterable
from typing import Optional

def mean(xs: Iterable[float], *, default: Optional[float] = None) -> float:
    """Arithmetic mean; return default if xs is empty."""
    xs = list(xs)
    if not xs:
        if default is None:
            raise ValueError("empty input")
        return default
    return sum(xs) / len(xs)

Comprehensions & generators

# dict comp
name_lengths = {name: len(name) for name in ["ada", "grace", "guido"]}

# generator expression (streaming)
def read_numbers(path):
    with open(path) as f:
        for line in f:
            if line.strip():
                yield float(line)

total = sum(x for x in read_numbers("data.txt") if x > 0)

Strings & f-strings

user, score = "alice", 42
print(f"{user=}, {score=}, ratio={score/50:.1%}")
csv = ",".join(["a", "b", "c"])
cols = " a  ,  b ,c ".replace(" ", "").split(",")

Files & JSON (use pathlib + context manager)

from pathlib import Path
import json

data = {"user": "alice", "score": 42}
p = Path("out") / "result.json"
p.parent.mkdir(parents=True, exist_ok=True)
p.write_text(json.dumps(data, indent=2))

loaded = json.loads(p.read_text())

Errors & context managers (EAFP)

from contextlib import suppress

try:
    value = d["key"]  # try it
except KeyError:
    value = "fallback"

# Ignore a specific error safely
with suppress(FileNotFoundError):
    Path("maybe.tmp").unlink()

Classes the simple way: dataclasses

from dataclasses import dataclass

@dataclass(slots=True)
class Point:
    x: float
    y: float

p = Point(3, 4)
print(p)  # Point(x=3, y=4)

CLI entry point

def main() -> int:
    # do stuff
    return 0

if __name__ == "__main__":
    raise SystemExit(main())

Standard library “power set”

collections: Counter, deque, defaultdict, namedtuple (less needed with dataclasses).

itertools: chain, islice, product, groupby, accumulate.

functools: lru_cache, partial, cached_property.

pathlib: paths everywhere.

datetime: datetime, timezone, timedelta; prefer fromisoformat/isoformat.

subprocess: run([...], check=True, capture_output=True, text=True).


Performance & correctness heuristics

Use dict and set for fast membership/counting; Counter for tallies.

Prefer generator pipelines for large data; materialize only when needed.

Avoid premature micro-opts; profile with timeit/cProfile when it matters.


Common pitfalls (memorize these)

❌ Mutable default args: def f(x, seen=[]) → use None then create inside.

Use is only for None: if x is None: not == None.

Integer division: / → float, // → floor.

Copying: a = b shares reference; use list(b)/b.copy()/copy.deepcopy.

Late binding in loops: funcs = [lambda i=i: i for i in range(3)].

Always close files → use with open(...) as f.


Minimal workflow (happy path)

1. Create env: python -m venv .venv && source .venv/bin/activate (Windows: .\.venv\Scripts\activate).


2. Install: pip install -U pip setuptools wheel.


3. Format/lint/test: black . && ruff . && pytest -q.




---
