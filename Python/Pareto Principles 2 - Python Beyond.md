1. **Environments & Dependencies**

* Per-project isolation with **venv**
* Install deps with **pip** (or faster **uv**)
* Global CLI tools via **pipx**
* **`pyproject.toml`** as the single source of truth for project metadata and tool config
* Basic dependency pinning/locking (requirements or uv/Poetry lock concept)

2. **Project Layout & Packaging (concepts)**

* `src/` layout; module vs package; role of `__init__.py`
* Build artifacts: **wheel** vs **sdist** (recognize)
* Semantic versioning; changelog; minimal build/publish flow

3. **Quality Gates (keep code healthy)**

* **Formatter**: Black *or* ruff-format (pick one)
* **Linter**: **ruff**
* **Type checker**: **Pyright** or **mypy**
* **pre-commit** to run format/lint/type locally

4. **Testing**

* **pytest** basics: discovery, assertions, fixtures (at a high level)
* **Coverage** measurement; target a simple threshold on CI

5. **Debugging, Logging, Profiling**

* **breakpoint()/pdb** mental model; stepping & inspecting
* Stdlib **logging**: levels, handlers; structured logs concept
* Perf triage: **timeit**, **cProfile**; know I/O-bound vs CPU-bound and memory hotspots

6. **Git & CI/CD**

* Branching, rebasing, PRs, tags
* Minimal **CI** (e.g., GitHub Actions): run format → lint → type → tests → build
* Keep **secrets** in CI secret store; don’t hardcode

7. **Configuration & Secrets**

* 12-factor mindset: config via **environment variables**
* Local **.env** files (do not commit); optional “settings” layer (e.g., Pydantic Settings concept)
* Key/secret rotation basics

8. **HTTP & APIs**

* **requests** or **HTTPX** (sync/async): sessions, timeouts, retries/backoff (concept)
* REST basics: status codes, pagination, auth (Bearer/OAuth2)

9. **Databases & Persistence**

* SQL essentials: queries, indexes, **transactions**
* **SQLite** for dev/tests; Postgres/MySQL in prod (concept)
* **SQLAlchemy** (ORM vs Core) + **Alembic** for migrations
* Caching layer concept (e.g., Redis)

10. **Web Backends (if you build services)**

* **FastAPI** (or Flask) basics; request/response lifecycle
* **ASGI/WSGI**; serving with **Uvicorn/Gunicorn**
* Validation/serialization mindset; CORS basics; background jobs (concept)

11. **Deployment & Ops**

* **Docker** fundamentals: small images, non-root, healthchecks
* Platforms: container services (Cloud Run/ECS) or serverless (Lambda) concepts
* Observability: metrics, tracing, **error monitoring** (Sentry-style)

12. **Security Hygiene**

* Never commit secrets; use env/secret managers
* Dependency scanning (**pip-audit**/Safety); static checks (**Bandit**)
* Web risks 101: SQLi, XSS, CSRF; rate limiting & input validation

13. **Documentation & DX**

* Great **README** (install, run, test, examples)
* Docstrings that match public API; doc site with **MkDocs**/**Sphinx** (recognize)
* Examples/notebooks policy (clean outputs; reproducible)

14. **Files, OS, & Subprocess**

* **pathlib** mental model (paths as objects); globbing
* **subprocess** basics: exit codes, timeouts; when to shell out

15. **Specialization Tracks (pick what fits your work)**

* **Data**: pandas (tables, joins, groupby), CSV vs Parquet, Arrow concept
* **Async/concurrency**: asyncio for I/O-bound; processes for CPU-bound; task queues (Celery/RQ) concept
* **CLI apps**: argparse (or Click/Typer) and console-scripts entry points
* **Scheduling**: cron/Task Scheduler; lightweight job runners

> Use this as a checklist. If a bullet doesn’t apply to your role, skip it; if it does, learn just enough to explain it in one minute and use it on a tiny example (no need for deep dives).
