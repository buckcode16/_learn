
Below is a **constraint-driven, core SDE syllabus** from zero → employable. It’s stack-pinned, single-project, and every phase has a **Definition of Done (DoD)** so there’s no ambiguity. It balances **practical engineering** (APIs, data, ops, security) with the **minimal DSA set** you’ll be asked to demonstrate.

# Ground rules (fixed)

- **Primary language:** TypeScript (Node 20).
    
- **DB:** PostgreSQL 15. **Cache/queue:** Redis 7.
    
- **HTTP:** Fastify (or Express), Zod for validation.
    
- **Infra (local):** Docker Compose; Nginx as reverse proxy.
    
- **Observability:** pino logs, Prometheus metrics, OpenTelemetry traces (local).
    
- **Repo:** single monorepo `core-sde/` with `apps/api`, `apps/worker`, `libs/`, `infra/`, `docs/`.
    
- **One capstone:** “Workboard” (boards/lists/cards/comments/uploads/activity feed). Minimal web UI (no framework deep-dive).
    
- **Daily cadence (5d/wk):**
    
    - Build: 90 min (timer; stop when it ends).
        
    - DSA: 30–45 min (one problem; tests; complexity stated).
        
    - Notes/Tests: 20 min (DoD checks).
        
- **Allowed references:** Postgres, Node, Fastify, Redis, OpenTelemetry, Docker, MDN docs. No tutorials/videos.
    
- **Every DoD includes:** unit/integration tests pass; types strict; lints clean; short design note in `docs/`; `docker compose up` stands the system.
    

---

# Competency targets (what “proficient” means)

- **APIs & Data:** correct CRUD, validation, **cursor pagination**, indexing, transactions, migrations, idempotency.
- **Reliability:** retries + jitter, **DLQ**, circuit breaker, rate limiting, backpressure.
- **Security:** auth (access/refresh), RBAC, input validation, secrets, CORS/CSRF, safe file handling.
- **Observability:** structured logs, SLIs/SLOs, metrics dashboards, basic traces.
- **Performance:** profiling, SQL plans, cache use, avoiding N+1, memory/CPU basics.
- **System design:** requirements, constraints, capacity, trade-offs, simple cost math.
- **DSA (minimal, interview-relevant):** arrays/strings/maps/sets; **binary search**; **two-pointers** (merge/intersect/window); **sliding window**; **stack/queue**; BFS on trees; heaps (priority queue) at awareness level.

---

## Phase 0 (Day 0): Environment & scaffolding (½ day)

**DoD:** Monorepo ready; Postgres/Redis via Docker; `pnpm test` green; CI lint/test workflow.

---

## Phase 1 (Week 1): Language & HTTP fundamentals

**Deliverable:** API skeleton with health, versioned routing, error envelope.  
**Scope:** strict TS config; Fastify plugin pattern; zod schemas; centralized error handler; request IDs; Nginx reverse proxy; basic k6 smoke test.  
**DSA (daily):** arrays/strings/maps/sets; write table-driven tests.  
**DoD:** `GET /v1/health` returns JSON with requestId; 95th percentile latency < 50 ms locally under 100 rps.

---

## Phase 2 (Week 2): Data modeling & SQL

**Deliverable:** schema + migrations for `users, boards, memberships, lists, cards, comments`.  
**Scope:** normalization, foreign keys, **indexes**: `(boards.owner_id)`, `(lists.board_id, position)`, `(cards.list_id, position, id)`, `(comments.card_id, created_at)`. Transactions for multi-row ops.  
**DSA:** **two-pointers** intersection & merge; reasoning about sort keys `(ts,id)`.  
**DoD:** `EXPLAIN ANALYZE` shows index use for card listing queries; migration up/down safe.

---

## Phase 3 (Week 3): CRUD + validation + **cursor pagination**

**Deliverable:** Boards/Lists/Cards CRUD with server-side **seek pagination**.  
**Scope:** `GET /cards?listId=&limit=&cursor=` (ordered by `position,id`); zod validation; error codes; optimistic concurrency via `updated_at` precondition.  
**DSA:** two-pointers merge (server page + client optimistic); binary search for insert position.  
**DoD:** paging test: insert during paging → no dupes/misses across 3 pages.

---

## Phase 4 (Week 4): Auth & session management

**Deliverable:** Email/password auth; **refresh rotation**; RBAC (owner/admin/member).  
**Scope:** hash passwords; refresh tokens table with rotation & revocation; middleware to enforce roles; **idempotent login** (same device).  
**DSA:** **sliding window** login limiter (per IP/user) in Redis; stack-style guard chain (short-circuit).  
**DoD:** token theft simulation invalidates old refresh; limiter holds at configured thresholds.

---

## Phase 5 (Week 5): Caching & rate limiting

**Deliverable:** Read-through cache for hot GET; **token bucket** limiter for write endpoints.  
**Scope:** cache keys & TTL/refresh-ahead; stampede protection (single-flight); `429` policy; cache invalidation on write.  
**DSA:** sliding window vs token bucket trade-offs; O(1) amortized operations with Redis Lua or atomic pipelines.  
**DoD:** cache hit-rate > 80% on hot key under synthetic load; limiter stable under bursts.

---

## Phase 6 (Week 6): Async jobs & reliability patterns

**Deliverable:** worker that processes activity events + email digest mock.  
**Scope:** queue library (bullmq or custom minimal) with **retries + exponential backoff + jitter + DLQ**; **idempotency keys**; outbox pattern for event emission.  
**DSA:** queue discipline; heap awareness for scheduling delays.  
**DoD:** replaying same event twice doesn’t duplicate side effects; DLQ captures poison messages with metadata.

---

## Phase 7 (Week 7): Files & streaming

**Deliverable:** file uploads for attachments via pre-signed URLs (local S3-compatible, e.g., MinIO).  
**Scope:** streaming uploads/downloads; content-type validation; size limits; antivirus stub; image thumbnail job.  
**DSA:** bounded buffers; backpressure via stream piping.  
**DoD:** uploads don’t buffer fully in memory; large file test passes; thumbnails generated by worker.

---

## Phase 8 (Week 8): Filtering & safe query compilation

**Deliverable:** advanced `GET /comments` filters: `author`, `date range`, `has:attachment`, `(tag:red OR tag:blue) AND not:archived`.  
**Scope:** parse infix → RPN (shunting-yard) → AST → parameterized SQL; guardrails (field allow-list; limit/timeout).  
**DSA:** **stack** (parser), **queue/BFS** (AST validation).  
**DoD:** bad filters 400 with clear errors; `EXPLAIN` shows bounded plans; injection attempts fail safely.

---

## Phase 9 (Week 9): Observability & SLOs

**Deliverable:** metrics dashboard and basic tracing.  
**Scope:** RED metrics (Rate, Errors, Duration); histogram buckets; per-route labels; error budgets + SLO doc; OpenTelemetry traces across API → DB → worker.  
**DSA:** none new; apply invariants to alert thresholds.  
**DoD:** dashboard screenshot in `docs/`; alert fires when p95 latency > SLO for 10 min.

---

## Phase 10 (Week 10): Performance & tuning

**Deliverable:** perf pass on hot paths.  
**Scope:** Node CPU/memory profiling; `clinic flame`; SQL plan tuning; batch queries; N+1 elimination; cache warming; pagination seek correctness under load.  
**DSA:** binary search lower/upper bounds; two-pointers for k-way merges where applicable.  
**DoD:** throughput +2× on target endpoint without regression (before/after graphs in `docs/`).

---

## Phase 11 (Week 11): Security & hardening

**Deliverable:** baseline security posture.  
**Scope:** CSP headers; CORS config; CSRF for cookie flows; secrets via env + rotation plan; audit log for auth & role changes; input size/time limits; PII handling.  
**DSA:** none new.  
**DoD:** OWASP “A-grade” checklist items documented; zap/burp basic scan clean.

---

## Phase 12 (Week 12): Delivery & deployment

**Deliverable:** containerized app deployed to a single PaaS (Render/Railway/Fly).  
**Scope:** health checks; startup/migration strategy; blue/green or rolling; seed script; rollback plan; cost estimate at target QPS.  
**DSA:** none new.  
**DoD:** one-command local run; public URL; smoke test suite green post-deploy.

---

## Phase 13 (Week 13): Testing depth

**Deliverable:** mature test suite.  
**Scope:** unit tests for handlers/utils; integration tests (API+DB) with test containers; contract tests for API (OpenAPI + schema checks); property-based tests (fast-check) for parsers; k6 load tests with thresholds.  
**DSA:** property-based tests over stack/queue/parser logic.  
**DoD:** >80% statements; flaky tests eliminated; load test meets SLO.

---

## Phase 14 (Week 14): System design drills & interview prep

**Deliverable:** three design docs + rehearsed walkthroughs.  
**Scope (drills):** URL shortener @ 10k rps; rate-limited API gateway; realtime comments over WebSocket (fan-out). Include requirements, scale numbers, storage, caching, failure modes, SLOs, cost.  
**DSA (daily 45 min):** final 10-problem set (arrays/strings, two-pointers, sliding window, stack/queue, simple BFS; 1–2 heap problems).  
**DoD:** each doc ≤2 pages + diagram; 20-min whiteboard narrative practiced.

---

# Minimal DSA set (fixed; 50 problems total)

- **Arrays/Strings (16):** dedup, group anagrams, longest substring (sliding window), rotate, merge intervals.
- **Hash/Set (8):** two-sum variants, frequency maps, dedup with stable order.
- **Binary Search (8):** lower/upper bound, search insert, peak element, “binary search on answer.”
- **Two-Pointers (10):** merge two/k sorted lists via successive merges, intersect postings lists, partition by predicate, visible window math.
- **Stack/Queue (6):** valid parentheses/RPN eval, monotonic stack min-window, BFS level order on a tree.
- **Awareness only (read/one implement):** heap/priority queue; union-find; trie (prefix search).

**Rules:** 1 problem/day; tests; complexity stated; stop at 45 minutes (log unfinished work).

---

# Promotion rubric (gate to “proficient”)

- **API correctness:** CRUD + pagination + validation + idempotency work under concurrency.
- **Data discipline:** indexes proven with `EXPLAIN`; transactions used where needed; no N+1.
- **Reliability:** retries with jitter; DLQ populated on poison messages; circuit breaker prevents cascades.
- **Security:** auth/refresh sound; inputs validated; secrets managed; uploads safe.
- **Observability:** dashboards + traces show request path; SLOs defined and enforced.
- **Performance:** hot endpoint < target p95; evidence of profiling-led fixes.
- **Design communication:** can present a design with constraints and trade-offs coherently.
- **DSA:** can implement and explain the minimal set under time pressure.
    

---

## Why this syllabus maximizes employability

- Uses **widely hired stack** (TS + Postgres + Redis + Docker) and **portable skills** (auth, pagination, caching, jobs, observability, testing, security).
- Integrates **DSA only where it pays off** (pagination, merging, filtering, throttling), matching how interviews and real systems overlap.
- Produces a **hiring-manager friendly artifact**: one repo with docs, tests, dashboards, and design notes that demonstrate end-to-end engineering, not just coding.