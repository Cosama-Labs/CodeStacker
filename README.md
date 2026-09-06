<p align="center">
  <img src="assets/codestacker-layers.svg" width="104" alt="Code Stacker layers icon">
</p>

# Code Stacker

> **One Function. One Folder. One Session.**

**Stop asking one AI coding session to be your entire engineering team. Assemble the team instead.**

Code Stacker is **not a framework** and it is not a requirement to become a software engineer before you can vibe code.

It is a practical **development approach** for founders, pure vibe coders, product people, and non-software engineers who can explain what the product should do but should not have to micromanage every implementation detail.

The idea is to structure the codebase so current AI coding agents are asked to do the kind of work they are best at: one bounded capability at a time, with a clear handoff before the next fresh session starts.

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

## The five problems this approach is trying to reduce

Code Stacker came from a simple observation: **AI coding agents often know how to write the code, but still lose the application.**

The approach is designed around five recurring failure modes.

| Pain point | What it looks like | Code Stacker response |
|---|---|---|
| **1. Context drift** | A long coding session forgets earlier decisions, changes direction, or stops honoring the original requirement. | **One function, one fresh session.** The function contract persists in `FUNCTION.md`; the implementation map persists in `DESIGN.md`. |
| **2. Fake-green completion** | The agent writes a test or script that passes, then declares the feature done even though the real user path never executes that code. | **One canonical entry point.** Tests, workers, and production must call the same function implementation. |
| **3. Scope wandering** | The coder notices unrelated code, refactors another feature, changes architecture, or breaks working areas while “helping.” | **Folder-bounded ownership.** One session owns one function boundary and does not modify sibling functions. |
| **4. The founder becomes the engineer** | A non-engineer is forced to specify classes, methods, queues, imports, and low-level implementation just to keep the AI on track. | **The founder owns WHAT; the coder owns HOW.** The founder defines purpose, input, output, constraints, and acceptance. The AI engineers the inside of that boundary. |
| **5. Features work alone but not together** | Payments work. Crawling works. The database works. The UI works. But the actual customer workflow never connects them correctly. | **Workflows own the handoff.** Explicit inputs/outputs connect bounded functions; dashboards launch workflows rather than reimplementing them. |

Code Stacker does not claim these problems disappear.

It tries to **change the shape of the work** so the AI has fewer opportunities to lose the plot.

> **Do not solve AI context problems with a bigger prompt. Solve them with smaller ownership boundaries and durable handoffs.**

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

## Organize it like a real product

Code Stacker defines **boundaries**, not one mandatory folder tree.

Functions can be grouped by business domain:

```text
functions/
├── email/
│   ├── send/
│   ├── read/
│   ├── search/
│   └── summarize/
├── customers/
│   ├── create/
│   ├── verify/
│   └── enrich/
├── billing/
│   ├── check_credits/
│   └── debit_credits/
└── publishing/
    ├── build/
    └── publish/
```

Shared external connectivity can live separately:

```text
integrations/
├── api/
│   ├── stripe/
│   ├── google_places/
│   └── resend/
├── mcp/
│   ├── twenty/
│   └── github/
├── cli/
│   └── crawl4ai/
├── models/
├── storage/
└── webhooks/
```

And user-facing workflows compose those bounded functions:

```text
workflows/
├── onboarding/
├── email_assistant/
├── publish_business/
└── billing/
```

This is intentionally flexible.

A small app may keep functions directly inside one workflow folder. A larger app may organize hundreds of functions by domain and reuse them across many workflows.

The hard rule is not the directory depth.

The hard rule is:

> **Keep business functions bounded, integrations explicit, handoffs documented, and orchestration in workflows.**

See [Project Structure](docs/PROJECT-STRUCTURE.md).

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
- [`docs/PROJECT-STRUCTURE.md`](docs/PROJECT-STRUCTURE.md) — flexible functions, workflows, integrations, UI, and platform structure
- [`docs/STACK-AND-INFRASTRUCTURE.md`](docs/STACK-AND-INFRASTRUCTURE.md) — DB, Redis, queues, APIs, MCP, models, storage
- [`templates/FUNCTION.md`](templates/FUNCTION.md) — function handoff contract
- [`templates/DESIGN.md`](templates/DESIGN.md) — function implementation map
- [`templates/INTEGRATION.md`](templates/INTEGRATION.md) — reusable API/MCP/CLI/model integration contract
- [`templates/pipeline.yaml`](templates/pipeline.yaml) — pipeline manifest
- [`START-HERE.md`](START-HERE.md) — plain-English guide for founders and pure vibe coders
- [`skills/`](skills/) — reusable AI coding skills for scoping, building, proving, documenting, integrating, and composing functions
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
