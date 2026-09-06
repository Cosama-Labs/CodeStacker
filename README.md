# Code Stacker

> **One Function. One Folder. One Session.**

**Stop asking one AI coding session to be your entire engineering team. Assemble the team instead.**

Code Stacker is an open architecture standard for building production software with AI coding agents. It breaks an application into small, contract-bound functions and treats each fresh coding session like one focused engineer on a software team.

Each function gets:

- **one responsibility**
- **one folder**
- **one declared input**
- **one declared output**
- **one `FUNCTION.md` handoff contract**
- **one `DESIGN.md` implementation map**
- **one coding session**

The pipeline acts as the shared team integration spec.

> **Build the function. Prove it works. Document the handoff. Stack it. Move on.**

---

## AI coding should work like a software team

Good engineering teams do not ask every developer to own the entire application.

One engineer may own payments. Another owns crawling. Another owns profile generation. Another owns publishing. They work independently against shared contracts and hand reliable inputs and outputs to the rest of the team.

Code Stacker applies the same operating model to AI coding agents.

```text
Product intent
     ↓
Pipeline / team spec
     ↓
Session 01 → 01_profile
Session 02 → 02_verify
Session 03 → 03_payment
Session 04 → 04_crawl
Session 05 → 05_review
     ↓
Integrated application
```

No session needs to remember the entire application.

---

## Why Code Stacker?

AI coding agents can write excellent code. The problem is maintaining reliable context across a large application long enough to preserve architecture, wire the real runtime path, honor prior requirements, and know when the job is actually complete.

Long coding sessions tend to create:

**context drift → scope creep → broken wiring → fake-green tests → forgotten requirements → “done” code that does not actually run**

Code Stacker changes the shape of the software problem.

> **If AI works best on small, well-defined coding problems, build the application out of small, well-defined coding problems.**

---

## The folder is the handoff

Every function folder is self-describing.

```text
05_crawl/
├── FUNCTION.md
├── DESIGN.md
├── contract.py
├── run.py
├── repository.py
├── providers/
└── tests/
```

### `FUNCTION.md` — what the function promises

The handoff contract explains:

- what the function does
- why it exists
- how to invoke it
- expected input
- expected output
- allowed side effects
- dependencies
- example input/output
- failure and edge behavior where relevant

### `DESIGN.md` — how the function is actually built

The implementation map explains:

- canonical entry point
- file map
- execution flow
- important classes/functions
- database reads/writes
- Redis/queue usage
- API/MCP/model calls
- data transformations
- invariants
- implementation decisions and why
- known edge cases
- where a future coder should make changes

A fresh coder should be able to enter the folder months later and understand the function without reading the previous AI chat or reverse-engineering the whole application.

> **The code is not the handoff. The folder is the handoff.**

---

## Outputs are contracts, not pass/fail boxes

A function output is whatever the next step needs.

It might be:

```text
customer record
crawl evidence IDs
classification + confidence
generated artifact
score
list of matches
approval_required
retry_after
published URL
database record ID
nothing but a durable side effect + verification reference
```

Operational execution state can still be tracked separately:

```text
queued → running → completed
                 ↘ failed
```

But **business output is not limited to pass/fail**.

The rule is simply:

```text
INPUT → FUNCTION → DECLARED OUTPUT → NEXT FUNCTION
```

---

## Core rules

1. **One function, one business responsibility.**
2. **One function, one folder.**
3. **One function, one AI coding session.**
4. **Every function folder contains `FUNCTION.md` and `DESIGN.md`.**
5. **The pipeline owns execution order.**
6. **Functions do not call sibling functions.**
7. **Functions communicate through explicit handoff contracts.**
8. **Outputs may be any declared result shape; they are not limited to pass/fail.**
9. **Large payloads are stored; references move through the pipeline.**
10. **The database owns durable state; Redis is not hidden business truth.**
11. **External APIs, MCP calls, model calls, queues, and storage remain bounded dependencies.**
12. **Tests, workers, and production call the same canonical function entry point.**
13. **Before ending a session, the coder updates `DESIGN.md` to match the code that actually exists.**

---

## Example application

```text
app/
└── onboard/
    ├── 01_profile/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── 02_verify/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── 03_payment/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── 04_crawl/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── 05_review/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── 06_build/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── 07_publish/
    │   ├── FUNCTION.md
    │   └── DESIGN.md
    ├── pipeline.py
    └── router.py
```

---

## The UI follows the same architecture

Code Stacker also maps cleanly to how users actually experience software.

A user does not see a giant backend architecture. They see **sections, actions, and workflows**.

A product surface can be thought of as:

```text
Dashboard
├── Create Profile      → starts profile workflow
├── Verify Business     → starts/resumes verification workflow
├── Crawl Website       → starts crawl workflow
├── Review Data         → opens review workflow
├── Build Package       → starts build workflow
└── Publish             → starts publish workflow
```

A dashboard is therefore not another business-logic layer. It is primarily a **workflow launcher and status surface**.

```text
UI button / link
      ↓
workflow / pipeline
      ↓
bounded function(s)
      ↓
declared output + durable state
      ↓
UI renders current result/state
```

This keeps frontend behavior aligned with backend architecture:

- navigation exposes user-facing capabilities;
- buttons start, resume, or act on workflows;
- dashboards show workflow state and outputs;
- business rules stay in bounded functions;
- the frontend does not invent a second orchestration system.

Not every internal function needs its own screen or public API. Several functions may compose one user workflow, and one function may have no direct UI at all.

> **The UI exposes capabilities. The pipeline coordinates them. The functions implement them.**

---

## The production-test rule

> **The test, worker, and pipeline execute the same canonical function.**

Bad:

```text
test script → Crawl4AI directly → SUCCESS

production route → create DB row → never calls crawler
```

Good:

```text
test     ─┐
pipeline ─┼→ 04_crawl.run(input) → Crawl4AI → DB → declared output
worker   ─┘
```

One implementation. One runtime path. No test-only version of success.

---

## Stack neutral by design

| Concern | Examples | Code Stacker rule |
|---|---|---|
| Backend | FastAPI, Express, NestJS, Go, Rails | Pipeline invokes canonical function entry points |
| Database | PostgreSQL, Supabase, MySQL, SQLite | Durable business state belongs in the database |
| Cache | Redis, Valkey | Cache/coordination only; no hidden canonical state |
| Queue | Celery, RQ, Dramatiq, BullMQ, SQS | Workers invoke the same canonical function |
| External API | Stripe, Google, Twilio, Crawl4AI | Provider adapter stays inside the owning boundary |
| AI / Model | OpenAI, xAI, local models, OpenRouter | Model call is a dependency of a bounded function |
| MCP | Any MCP server | Runtime dependency, not application orchestration |
| Storage | S3, R2, Supabase Storage | Store large artifacts; pass IDs/URIs |
| Frontend | React, Next.js, Vite, mobile | UI talks to the workflow, not individual function APIs |

See [Stack and Infrastructure](docs/STACK-AND-INFRASTRUCTURE.md).

---

## Not another prompting framework

Code Stacker is **not** a mega-prompt, agent framework, orchestration library, coding checklist, or replacement for your stack.

It is a **software architecture and team operating model for AI coding**.

> **The AI does not need to remember your architecture. Your architecture remembers it for the AI.**

---

## Repository contents

- [`spec/CODE-STACKER-v0.1.md`](spec/CODE-STACKER-v0.1.md) — full draft standard
- [`docs/AI-CODING-WORKFLOW.md`](docs/AI-CODING-WORKFLOW.md) — one-function / one-session operating method
- [`docs/STACK-AND-INFRASTRUCTURE.md`](docs/STACK-AND-INFRASTRUCTURE.md) — DB, Redis, queues, APIs, MCP, models, storage
- [`templates/FUNCTION.md`](templates/FUNCTION.md) — function handoff contract
- [`templates/DESIGN.md`](templates/DESIGN.md) — function implementation map
- [`templates/pipeline.yaml`](templates/pipeline.yaml) — pipeline manifest
- [`examples/yello/`](examples/yello/) — real pipeline example
- [`examples/mixed-stack/`](examples/mixed-stack/) — mixed-runtime example

---

## Status

**v0.1 — Draft standard**

Code Stacker is intended to be implemented, challenged, broken, improved, and refined in public.

## In one sentence

> **Stop asking AI to build your application. Stack working functions instead.**

## License

Apache License 2.0. See [LICENSE](LICENSE).
