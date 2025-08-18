
---

# The protocol formula (use this to reason about any expression)

1. **Identify the receiver**

   * Literal/object made → e.g. `" "` or `["a","b"]`
   * Note **mutability** (immutables return new objects; mutables often mutate & return `None`)

2. **Classify the syntax**

   * **Method call:** `obj.m(args)`
   * **Operator:** `a OP b` (e.g., `%`, `+`, `in`, `[]`)
   * **Builtin:** `len(x)`, `bool(x)`, `iter(x)`, `str(x)`, `format(x)`, etc.
   * **Control/statement:** `for`, `with`, `==`, slicing, assignment to `[]`, etc.

3. **Desugar to data-model calls**

   * **Method:** `obj.m(a, b)` ≈ `type(obj).m(obj, a, b)`
   * **Binary op:** `a OP b` → `a.__op__(b)` → if `NotImplemented`, `b.__rop__(a)`
   * **In-place op:** `a OP= b` → `a.__iop__(b)` → fallback to `__op__`
   * **Subscription:** `x[i]` → `x.__getitem__(i)`; `x[i]=v` → `__setitem__`; `del x[i]` → `__delitem__`
   * **Membership:** `y in x` → `x.__contains__(y)`; fallback to iterate
   * **Iteration / `for`:** `iter(x)` → `x.__iter__()` → then repeatedly `it.__next__()`

     * Sequence fallback: `x.__getitem__(0..)` until `IndexError`
   * **Length / truth:** `len(x)` → `x.__len__()`; `bool(x)` → `x.__bool__()` else `__len__()>0`
   * **Compare:** `==,<,<=,>,>=` → `__eq__`, `__lt__`, … (with reflected fallbacks)
   * **Callables:** `f(...)` → `f.__call__(...)`
   * **Context manager:** `with x as y:` → `x.__enter__()`; on exit `x.__exit__(exc_type, exc, tb)`
   * **String/format:** `str(x)` → `x.__str__()`; `repr(x)` → `__repr__()`;
     `format(x, spec)` / f-strings → `x.__format__(spec)`

4. **Apply type expectations & fast paths**

   * If a method expects specific element types (e.g., `str.join` expects **all str**), it validates and raises `TypeError` if violated.
   * Built-ins often special-case: empty / single element / precompute sizes for one allocation.

5. **Return / side-effects**

   * **Immutables** (`str`, `tuple`, `int`, …): return **new** objects.
   * **Mutables** (`list`, `dict`, `set`): many mutating methods return `None`.

6. **(Optional) Functional wrapper**

   * `operator.mod(a,b)` ≡ `a % b`; `operator.getitem(x,i)` ≡ `x[i]`; handy when you need a callable.

---

## Apply the formula to `" ".join(["a","b"])`

* (1) Receiver: `" "` (`str`, **immutable**).
* (2) Method call.
* (3) Desugar: `str.join(" ", ["a","b"])`; inside, it uses **iteration protocol** on the list and validates items are `str`.
* (4) Fast path: two items ⇒ compute size, one allocation.
* (5) Return: a **new** `str` `"a b"` (no mutation).
* (6) Operator wrappers: not used here, but conceptually unnecessary.

---

## Pocket cheat-sheet (memorize these equalities)

* `obj.m(*a)` ≈ `type(obj).m(obj, *a)`
* `a + b` ≈ `a.__add__(b)` or `b.__radd__(a)`
* `a[i]` ≈ `a.__getitem__(i)`; `a[i]=v` ≈ `a.__setitem__(i, v)`
* `for x in y` ≈ `it = iter(y); next(it) ...`
* `len(x)` ≈ `x.__len__()`
* `bool(x)` ≈ `x.__bool__()` or `x.__len__()>0`
* `with x:` ≈ `x.__enter__()` / `x.__exit__(...)`
* `f(...)` ≈ `f.__call__(...)`


---

# What “protocol” means (in Python)

A **protocol** is a behavior contract defined by special (dunder) methods. You **use syntax or builtins**, and Python calls those dunders for you.

* You write `len(x)` → Python calls `x.__len__()`.
* You write `for y in x:` → Python calls `iter(x)` → `x.__iter__()` (then `__next__()` on the iterator).
* You write `x[i]` → Python calls `x.__getitem__(i)`.

Think: *syntax → protocol → dunder(s)*.

---

# The protocol-first checklist (use this to reason about any expression)

1. **What form is this?**

   * **Builtin** (`len`, `bool`, `iter`, `reversed`, `hash`, `str`, `format`)
   * **Operator** (`+`, `%`, `in`, `[]`, comparisons, `+=`)
   * **Method call** (`obj.method(...)`)

2. **Which protocol does it trigger?**

   * **Iteration:** `iter(x)` → `__iter__()` (or sequence fallback via `__getitem__(0…)`), then `__next__()`.
   * **Sizing / truthiness:** `len(x)` → `__len__()`; `bool(x)` → `__bool__()` else `__len__()>0`.
   * **Containment:** `y in x` → `x.__contains__(y)`; if absent, iterates.
   * **Indexing/slicing:** `x[i]` → `__getitem__(i)`; `x[i]=v` → `__setitem__`; `del x[i]` → `__delitem__`. Slices pass a `slice(...)` object.
   * **Numeric ops:** `a OP b` → `a.__op__(b)` → if `NotImplemented`, `b.__rop__(a)`; `a OP= b` → `__iop__` then fallback.
   * **Compare:** `__eq__`, `__lt__`, etc. Sorting mainly needs `__lt__`.
   * **Callable:** `f(...)` → `f.__call__(...)`.
   * **Context manager:** `with x:` → `x.__enter__()` / `x.__exit__(...)`.
   * **String/formatting:** `str(x)` → `__str__()`; `repr(x)` → `__repr__()`; `format(x,spec)` / f-strings → `__format__(spec)`.

3. **Mutates or returns new?**

   * **Immutables** (`str`, `tuple`, `int`, `range`, `frozenset`) return **new objects**.
   * **Mutables** (`list`, `dict`, `set`) methods often **mutate** and return `None`. Operators like `+=` may call `__iadd__` (in-place) or create a new object if not supported.

4. **Type expectations & errors**

   * Protocols may require element types (e.g., `str.join` needs all `str`), else `TypeError`.
   * Division by zero → `ZeroDivisionError`; missing key → `KeyError`; missing index → `IndexError`.

That’s it. Use steps 1–4 to “decode” behavior without memorizing internals.

---

## Apply it quickly to two common cases

* `" ".join(["a","b"])`

  1. Method call on a **str** (immutable).
  2. Uses **iteration protocol** on the list and validates all items are `str`.
  3. Returns a **new** string.
  4. Non-str element → `TypeError`.

* `item in my_set`

  1. `in` operator.
  2. **Containment protocol** → `my_set.__contains__(item)` (hash lookup).
  3. No mutation.
  4. Unhashable item (e.g., `list`) → `TypeError`.

---

## Need-to-know 80/20 rules

* **Never call dunders directly.** Use the syntax/builtin; let Python dispatch.
* **Prefer absolute imports and run tools from project root** (keeps protocols consistent across tools).
* **For performance:** build strings with `''.join(...)`; iterate, don’t index in loops unless you need the index (`enumerate`).
* **Comparisons:** define `__eq__` and `__lt__` if you need sorting; keep them consistent with `__hash__` if objects go in sets/dicts.

---

## 60-second drills (do, don’t copy)

1. For each line, name the protocol:
   `len(x)`, `x[2:5]`, `v in x`, `for y in x`, `x += y`, `with lock: ...`.
2. Predict mutate/new:
   `lst.extend([3])`, `s + "x"`, `d.update(k=1)`, `t + (3,)`.
3. Explain in one sentence why `"".join(map(str, items))` is used instead of a `for` loop with `+=`.


---

# 1) Two kinds of behavior

* **Protocols (syntax/builtins → dunder methods).**
  You write a *syntax* or call a *builtin*; Python dispatches to a special method.
* **Normal methods (dot calls on objects).**
  You call `obj.method(...)`; no special magic unless that method itself uses a protocol on its inputs.

# 2) The 8 core protocols (cover \~90% of day-to-day Python)

Memorize the left column; the right is what Python calls under the hood.

1. **Iteration / loops**
   `for x in xs` → `iter(xs)` → `xs.__iter__()` → iterator `.__next__()`

2. **Length & truthiness**
   `len(x)` → `x.__len__()`
   `if x:` → `x.__bool__()` else `x.__len__()>0`

3. **Containment**
   `y in x` → `x.__contains__(y)` (else falls back to iterating)

4. **Indexing / slicing / assignment**
   `x[i]` → `x.__getitem__(i)` (slice uses a `slice(...)` object)
   `x[i] = v` → `x.__setitem__(i, v)`
   `del x[i]` → `x.__delitem__(i)`

5. **Numeric operators**
   `a + b` / `a - b` / `a % b` → `a.__add__/__sub__/__mod__(b)`
   fallback to `b.__radd__/__rsub__/__rmod__(a)`
   `a += b` etc. try `__iadd__` (in-place) first

6. **Comparisons / ordering**
   `a == b` / `<` / `<=` / `>` / `>=` → `__eq__`, `__lt__`, …
   (sorting mainly needs `__lt__`)

7. **Callables**
   `f(... )` → `f.__call__(...)` (lets objects behave like functions)

8. **Context managers**
   `with obj:` → `obj.__enter__()`; on exit → `obj.__exit__(exc_type, exc, tb)`

Nice-to-know later (still fundamental):

* **String/formatting:** `str(x)` → `__str__`, `repr(x)` → `__repr__`, `format(x,spec)` / f-strings → `__format__`.
* **Attribute access hooks:** `__getattr__`, `__getattribute__`, `__setattr__` (advanced; skip for now).

# 3) Normal methods (not protocols)

Examples: `str.join`, `list.append`, `dict.get`, `set.add`.
You call them directly: `obj.method(...)`. They **may** internally *use* protocols on their arguments (e.g., `str.join` iterates its iterable argument), but *you* are not invoking a dunder.

# 4) Quick rule-of-thumb to decide “protocol vs method”

* If you see **syntax** or a **builtin** (`len`, `iter`, `bool`, operators, `[]`, `in`, `with`, call `()`): **protocol → dunder**.
* If you see a **dot call** (`obj.method(...)`): **normal method** (unless it’s a callable object and you’re doing `obj(...)`, which is `__call__`).

# 5) One practical implication to remember

* **Immutables** (`str`, `tuple`, `int`, `range`, `frozenset`) → operations return **new objects**.
* **Mutables** (`list`, `dict`, `set`) → many methods **mutate** and return `None`.
  (Operators like `+=` may mutate if `__iadd__` exists; otherwise they create a new object.)

---

### 30-second self-check

Classify each as protocol or normal method, and name the dunder if protocol:

* `len(usernames)`
* `items[2:5]`
* `email in seen`
* `" ".join(parts)`
* `nums += more`
* `with lock:`

---

## Core idea

* **Everything in Python is an object.** Literals like `42`, `"hi"`, `[1,2]` are just **instances** of built-in classes (`int`, `str`, `list`, …).
* **Any object** (built-in or user-defined) can expose:

  * **Normal methods** (you call with a dot): `obj.method(...)`
  * **Special methods (dunders)** that power **protocols** triggered by syntax/builtins: `len(x)`, `x[i]`, `a+b`, `in`, `for`, `with`, etc.

So, normal methods are **not** only for “data/literals/primitive types”; they’re for **any class** that defines them (yours or Python’s). And yes, operators/builtins invoke **protocols → dunders**.

## Rule-of-thumb (minimal)

1. **Dot call** → normal method on that object’s class.
   Examples: `str.join`, `list.append`, `dict.get`, your own `obj.save()`.
2. **Syntax / special builtins** → protocol → dunder.

   * `len(x)` → `x.__len__()`
   * `for y in x` → `x.__iter__()` → `__next__()`
   * `y in x` → `x.__contains__(y)` (or iteration fallback)
   * `x[i]` / `x[i]=v` → `__getitem__` / `__setitem__`
   * `a + b`, `a % b`, … → `__add__`, `__mod__`, with reflected/in-place variants
   * `with m:` → `m.__enter__` / `m.__exit__`
   * `f(...)` → `f.__call__`

## Tiny demo to cement it

```python
class Bag:
    def __init__(self): self._items = []
    def add(self, x): self._items.append(x)         # normal method (dot call)
    def __contains__(self, x): return x in self._items  # protocol for `in`

b = Bag()
b.add(3)             # dot → normal method
3 in b               # syntax → protocol → b.__contains__(3)
```

## Quick implications

* **Use syntax/builtins** (let Python call the dunder); don’t call dunders directly.
* **Immutables** (e.g., `str`, `tuple`, `int`) return **new** objects; **mutables** (e.g., `list`, `dict`, `set`) often **mutate** and return `None`.
* Built-ins like `len/iter/bool` are protocol triggers; built-ins like `sum/max/min` are regular functions (they may use protocols internally, but you’re not invoking a dunder directly).


---

Think of these as **two orthogonal axes** (Protocol/Normal Method VS Mutability):

## Axis 1 — *How it’s invoked*

* **Protocol (syntax/builtins → dunders):** `len(x)`, `x[i]`, `a+b`, `a+=b`, `in`, `for`, `with` …
  Python dispatches to `__len__`, `__getitem__`, `__add__`, `__iadd__`, `__contains__`, `__iter__`, `__enter__/__exit__`, etc.
* **Normal method (dot call):** `obj.method(...)` like `list.append`, `dict.update`, `str.replace`.

## Axis 2 — *What effect it has*

* **Immutable types** (`str`, `int`, `tuple`, `range`, `frozenset`): can’t change in place → operations **return new objects**.
* **Mutable types** (`list`, `dict`, `set`): can change in place → many mutating methods **modify and return `None`**.
  Operators may mutate **if** an in-place dunder exists (e.g., `__iadd__`); otherwise they create a new object.

These axes are **independent**. Mutability isn’t a protocol; it just determines the **effect** once a protocol/method runs.

---

## Quick cross-examples (to cement the separation)

* **Protocol + immutable → new object**

  ```python
  s = "a";  s2 = s + "b"        # __add__ → new str
  s = "a";  s  = s.replace("a","b")  # normal method, also returns new (immutable)
  ```

* **Protocol + mutable → can mutate or not**

  ```python
  xs = [1,2];  ys = xs + [3]    # __add__ → new list (xs unchanged)
  xs = [1,2];  xs += [3]        # __iadd__ → mutates xs in place
  ```

* **Normal method + mutable → usually mutates**

  ```python
  xs = [1,2];  xs.append(3)     # mutates, returns None
  d = {"a":1}; d.update(b=2)    # mutates, returns None
  ```

* **Sets/dicts show both flavors**

  ```python
  A|B      # new set (protocol __or__)
  A |= B   # mutate A (protocol __ior__)
  A.union(B)   # new set (normal method)
  A.update(B)  # mutate A (normal method)
  ```

---

## Rules of thumb

1. First ask: **protocol or normal method?** (how it’s invoked)
2. Then ask: **type mutable or immutable?** (what effect you’ll see)
3. Expect:

   * Mutating methods on mutables return `None`.
   * Non-mutating operations return a **new** object (even on mutables).
   * `+=` tries in-place (`__iadd__`); if absent, falls back to new (`__add__`).


---
## Notes
#### Must-commit to memory
Mutability & Immutability
- numbers (int, float, bool), string, tuples, frozenset, range are immutable
- list, dict, set are mutable

`is` checks same object, `==` checks value
	-Use `is None` never `== None`

Truthiness
`0`, `0.0`, `""`, `[]`, `{}`, `set()`, `range(0)` are falsy; most others are truthy.

Copying
`x.copy()`, `x[:]`, `dict(x)` make **shallow** copies; nested objects are shared. 

---
Special methods (dunder methods)
- operators are special method

```

For instance the operator "%"
	a % b == a.__mod__(b)

The are also something called in-place operator
	a %= b == a.__imod__(b) (falls back to __mod__)

operator is a module and it gives callable wrapper:
// operator.mod(a,b) is a callable wrapper
operator.mod(a,b) === operator.__mod__(a,b) === a % b
the callable wrapper are for passing around as functions, not directly called
```

When would we need callable wrapper?
- When we need a callable (e.g., in high-order functions) instead of a symbol
```
import operator
xs, ys = [10,11,12],[3,5,7]
rema = list(map(operator.mod, xs, ys)) // cannot replace with just "%"
```

