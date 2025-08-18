
1. **Syntax bedrock**

* Indentation & blocks; statements vs expressions; comments & docstrings
* Literals & identifiers (numbers, strings, bytes, bool, `None`)
* Operators: arithmetic, comparison (incl. chained), boolean, membership `in`, identity `is`, bitwise (recognize)
* Assignment: simple, augmented, multiple/tuple unpacking, **walrus `:=`**
* Truthiness & short-circuiting (`and`/`or` return operands)

2. **Object model & scope**

* Names bind to objects; mutability vs immutability; reference semantics
* **LEGB** name resolution; `global`/`nonlocal` (what they do)
* Identity vs equality (`is` vs `==`); aliasing vs copies (shallow/deep)

3. **Core built-ins**

* Daily types: **`str`, `list`, `tuple`, `dict`, `set`, `bool`, `NoneType`**
* Indexing & slicing (start/stop/step; negative indices)
* Hashability & keys (immutable keys for dict/set)
* **Text vs bytes**; encodings at a high level (UTF-8 mindset)

4. **Control flow**

* `if / elif / else`, **ternary** expression
* `for` (over any iterable) and `while`; `break` / `continue` / loop `else`

5. **Comprehensions & generator expressions**

* List/dict/set comprehensions (filters, eval order)
* Generator expressions (lazy, single-pass) and when to prefer them

6. **Functions & parameters**

* Defining functions; **docstrings** as the contract
* Parameter kinds: positional-only, positional-or-keyword, keyword-only; `*args` / `**kwargs`
* Defaults evaluated once; mutable-default pitfall
* Closures & late binding (recognize); `lambda` (expression form)

7. **Exceptions**

* `try / except / else / finally` (purpose of each)
* Raising & propagation; catch narrowly; simple custom exception classes

8. **Modules, packages, imports**

* Import executes once then caches; absolute vs relative imports; aliasing
* Package layout (dir as package; `__init__` role)
* Script entry guard: **`__name__ == "__main__"`**

9. **Classes & data model (minimum)**

* Class vs instance attributes; method binding (`self`)
* Initialization intent; invariants live in init (concept)
* Representations: `__repr__` vs `__str__`
* Equality & hashing (value vs identity); effects on dict/set membership
* `@property` for computed/read-only attributes

10. **Dataclasses (plain data)**

* Auto `__init__/__eq__/__repr__`; when to choose over hand-rolled classes

11. **Iteration protocol**

* Iterable vs iterator; `for` uses `iter()`/`next()` under the hood; single-use iterators

12. **Generators**

* `yield` for lazy sequences; lifecycle; **`yield from`** (delegation concept)
* Why laziness (memory & composability)

13. **Context managers**

* `with` enter/exit protocol; guaranteed cleanup; common uses (files, locks, transactions)

14. **Strings & formatting**

* **f-strings** (interpolation; basic `!r` idea); format mini-language at a glance (width/alignment/precision)

15. **File I/O (language-level)**

* Text vs binary mode; newline handling; buffering (concept)
* Paths vs open file objects; always think about **encoding**

16. **Modern syntax to recognize**

* **Pattern matching `match/case`** (structural patterns; guards; no fallthrough)
* **Typing annotations**: function/variable annotations; `A | B` unions; built-in generics (`list[int]`)—for tooling, not runtime
* **Async basics**: `async def` / `await`; when I/O-bound work benefits (recognize only)

17. **Pythonic idioms**

* **EAFP** over LBYL (use exceptions when appropriate)
* Prefer `enumerate`/`zip`; use `with` for resources; prefer comprehensions for transforms

18. **Pitfalls to memorize**

* Mutable default arguments
* `is` vs `==`
* Reference vs copy; shallow vs deep (esp. nested)
* List-multiplication aliasing with nested lists
* Floating-point precision (tolerance comparisons)
* Comprehension/closure scope & evaluation order
* Short-circuit returns operands (impacts default-value patterns)
