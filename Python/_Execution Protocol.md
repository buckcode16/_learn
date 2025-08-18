
# Execution protocol — expanded

## 1) Session loop (fixed, repeatable)

* **T1 Read (10–15 min):** skim doc page; extract 3–5 APIs to use.
* **T2 Implement (35–50 min):** micro-kata(s) or a feature on a project.
* **T3 Quality gate (10–15 min):** format → lint → type → test → commit.
* **T4 Log (2 min):** write what you learned and next step.

## 2) Topic loop (per doc page)

* Identify **target APIs** (example for `pathlib`): `Path`, `glob`, `read_text`, `write_text`, `rename`.
* Draft **usage notes** in your own words (≤5 bullets).
* Write **1–3 micro-katas** that exercise the APIs.
* Add **tests first**, then code.

## 3) Micro-katas (practice catalog)

Pick 1–3 per session; keep each ≤40 lines.

* `Counter` word frequency across a directory; top-N; case-folding.
* `defaultdict(list)` group log lines by date, then by status code.
* `itertools.groupby` aggregate CSV rows by key; compute sums/means.
* `itertools.islice` sliding window (n=3) over sensor readings.
* `zip_longest` merge two unequal CSVs by index with fill values.
* `pathlib.Path.rglob` find duplicates by size → hash shortlist.
* `argparse` build CLI: `clean-names --in . --dry-run`.
* `json` + `datetime` serialize/deserialize records with ISO 8601.
* `csv.DictReader` normalize headers; write canonical CSV.
* `re` extract emails/URLs from text; compile patterns; measure perf.
* `logging` rotate logs; different levels for lib vs. CLI.
* `functools.lru_cache` memoize pure function; measure speedup.
* `dataclasses` define config; load from TOML/JSON; type-check.
* `subprocess.run` safe wrapper; capture output; timeout handling.
* `concurrent.futures` map CPU-bound function; compare serial vs. parallel.

**Definition of done (per kata):**

* Tests: ≥1 happy path, ≥1 edge case.
* Type hints present; `mypy` clean.
* `black` + `ruff` clean.
* Single commit with concise message.

## 4) Project loop (feature-by-feature)

* Open a **ticket** (one sentence).
* Write a **test** that fails.
* Implement minimal code to pass.
* Run quality gate.
* Commit: `feat: <ticket>` or `fix: <ticket>`.

## 5) Quality gate (exact commands)

```
python -m venv .venv && . .venv/bin/activate
python -m pip install -U pip
pip install -e .  # if package
black .
ruff .
mypy .
pytest -q
```

## 6) Repository scaffold (use this for every practice repo)

```
repo/
  pyproject.toml
  src/<pkg>/
    __init__.py
    cli.py
    core.py
  tests/
    test_cli.py
    test_core.py
  README.md
```

### Minimal `pyproject.toml`

```toml
[project]
name = "practice"
version = "0.1.0"
requires-python = ">=3.11"

[project.scripts]
practice-cli = "pkg.cli:main"

[tool.black]
line-length = 88

[tool.ruff]
line-length = 88
select = ["E","F","I","B","UP","N","SIM","PL"]

[tool.mypy]
python_version = "3.11"
disallow_untyped_defs = true
warn_return_any = true
warn_unused_ignores = true
strict_optional = true
```

## 7) Test skeletons

### Parametrized unit test

```python
# tests/test_core.py
import pytest
from pkg.core import top_n_counts

@pytest.mark.parametrize(
    "items,n,expected",
    [
        (["a","b","a","c","a","b"], 2, [("a",3),("b",2)]),
        ([], 3, []),
        (["x"], 5, [("x",1)]),
    ],
)
def test_top_n_counts(items, n, expected):
    assert top_n_counts(items, n) == expected
```

### CLI test

```python
# tests/test_cli.py
import subprocess, sys, json, pathlib

def test_cli_help():
    r = subprocess.run([sys.executable, "-m", "pkg.cli", "-h"], capture_output=True, text=True)
    assert r.returncode == 0
    assert "usage:" in r.stdout

def test_cli_runs(tmp_path: pathlib.Path):
    (tmp_path / "a.txt").write_text("A a a b\n")
    r = subprocess.run([sys.executable, "-m", "pkg.cli", str(tmp_path)], capture_output=True, text=True)
    assert r.returncode == 0
    data = json.loads(r.stdout)
    assert data["counts"]["a"] == 3
```

## 8) CLI pattern (`argparse`)

```python
# src/pkg/cli.py
from __future__ import annotations
import argparse, json, collections, pathlib

def parse_args() -> argparse.Namespace:
    p = argparse.ArgumentParser()
    p.add_argument("root", type=pathlib.Path)
    p.add_argument("--ext", default=".txt")
    return p.parse_args()

def run(root: pathlib.Path, ext: str = ".txt") -> dict[str, int]:
    counts: collections.Counter[str] = collections.Counter()
    for p in root.rglob(f"*{ext}"):
        counts.update(p.read_text(encoding="utf-8", errors="ignore").lower().split())
    return dict(counts)

def main() -> None:
    ns = parse_args()
    result = {"counts": run(ns.root, ns.ext)}
    print(json.dumps(result, ensure_ascii=False))

if __name__ == "__main__":
    main()
```

## 9) Commit discipline

* One logical change per commit.
* Message format: `feat|fix|refactor|test: short summary`.
* No broken main branch: tests must pass before merge.

## 10) Metrics (track weekly)

* **Throughput:** katas completed (target: 10–15).
* **Coverage proxy:** tests per module (target: ≥3 per module).
* **Quality:** `ruff`/`mypy` errors (target: zero).
* **Latency:** time from test-red to green (target: ≤30 min for katas).
* **Reuse:** helpers promoted from kata → `src/pkg/core.py`.

## 11) Review loop

End of week: pick **one** kata, refactor to readability; add types, docstrings, and a short README example. Archive learnings in a `CHANGELOG.md`.

## 12) Anti-patterns to avoid

* Reading without coding in the same session.
* Large, untested scripts.
* One-off notebooks for fundamentals (stick to modules + tests).
* Skipping the quality gate “just this once”.

