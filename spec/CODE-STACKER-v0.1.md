# Code Stacker v0.1
## A Function-Bounded Architecture Standard for AI-Assisted Software Development

**Status:** Draft Standard  
**Core rule:** One Function. One Folder. One Session.

---

## 1. Purpose

Code Stacker defines a practical way to build production software with AI coding agents by structuring the application the way a healthy engineering team structures work.

Instead of asking one long AI session to understand and modify an entire application, the system is divided into bounded functions with explicit handoffs. Each function is assigned to a fresh coding session. Workflows compose those functions. Integrations provide reusable connectivity. The UI starts or resumes workflows and renders their state.

The standard is intentionally:

- strict about boundaries;
- strict about handoffs;
- strict about the canonical production path;
- flexible about language, framework, database, transport, folder depth, provider, and UI technology.

---

## 2. Normative Language

The words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are normative.

---

## 3. The Team Model

Code Stacker treats AI coding sessions like engineers on a software team.

A normal software team may divide ownership like this:

```text
Engineer A → payments
Engineer B → email
Engineer C → crawling
Engineer D → profile generation
Engineer E → publishing
```

The engineers do not need identical internal implementations. They do need agreed interfaces and reliable handoffs.

Code Stacker applies the same model:

```text
Product intent
      ↓
Workflow / team specification
      ↓
Session A → bounded function
Session B → bounded function
Session C → bounded function
      ↓
Integrated application
```

The coding session is an architectural boundary.

---

## 4. Core Principles

1. A function MUST own one bounded business capability.
2. A function MUST have a clear folder boundary.
3. A function change SHOULD be implemented in one fresh AI coding session.
4. Every function folder MUST be self-describing.
5. Every function MUST declare its input and output.
6. Function output is a business result and MUST NOT be restricted to pass/fail.
7. Functions MUST NOT orchestrate sibling functions.
8. Workflows own sequence, branching, pauses, retries, and composition.
9. Integrations own connectivity, not product business rules.
10. The UI SHOULD invoke workflows rather than reimplement workflow logic.
11. Tests, workers, and production MUST use the same canonical function entry point.
12. The repository MAY be organized flexibly as long as these boundaries remain explicit.

---

## 5. Function Unit

A **function** is the smallest independently owned business capability in a Code Stacker application.

Examples:

- send an email;
- search email;
- verify a business;
- check credits;
- crawl a website;
- classify content;
- generate a package;
- publish an artifact;
- create an invoice;
- calculate a score.

A function is not required to be a language-level function. It may contain multiple files, classes, helpers, repositories, adapters, and tests.

The boundary is the business responsibility.

### 5.1 Function folder

Recommended:

```text
send/
├── FUNCTION.md
├── DESIGN.md
├── contract.*
├── run.*
├── repository.*
├── providers/
└── tests/
```

Only `FUNCTION.md` and `DESIGN.md` are standardized names in v0.1. Implementation filenames MAY vary by stack.

---

## 6. FUNCTION.md — The Handoff Contract

Every function folder MUST contain `FUNCTION.md`.

`FUNCTION.md` describes **what the function promises**.

It SHOULD include:

- purpose;
- how to use/invoke it;
- input;
- output;
- allowed side effects;
- external dependencies;
- example input/output;
- failure and edge behavior where relevant;
- data ownership;
- UI/workflow relationship;
- forbidden responsibilities.

A fresh engineer or AI coding session SHOULD be able to understand the public behavior of the function from this file without reconstructing the entire application.

> **The code is not the handoff. The folder is the handoff.**

---

## 7. DESIGN.md — The Implementation Map

Every function folder MUST contain `DESIGN.md`.

`DESIGN.md` describes **how the current code actually fulfills the function contract**.

It SHOULD include:

- canonical entry point;
- file map;
- execution flow;
- important symbols/classes/functions;
- database reads/writes;
- Redis usage;
- queue/worker behavior;
- API calls;
- MCP calls;
- CLI calls;
- model calls;
- storage behavior;
- data transformations;
- invariants;
- error handling;
- tests;
- implementation decisions and why;
- common change locations;
- known limitations;
- handoff notes.

A coding session MUST update `DESIGN.md` before completion if the implementation changed.

`DESIGN.md` MUST describe the code that exists, not the design the coder intended to build.

### 7.1 Contract vs design

```text
FUNCTION.md = WHAT this function promises
DESIGN.md   = HOW this implementation currently works
```

Changing implementation details does not necessarily change `FUNCTION.md`.

Changing public input/output or business behavior usually does.

---

## 8. Input and Output

Every function MUST declare input and output.

Example:

```yaml
input:
  business_id: uuid
  website_url: url

output:
  crawl_run_id: uuid
  evidence_ids: uuid[]
  page_count: integer
```

### 8.1 Output is not pass/fail

A function output may be:

- a record;
- a list;
- an ID;
- a score;
- a classification;
- a generated artifact;
- a URL;
- a set of evidence references;
- a user-action requirement;
- retry information;
- a durable side effect plus a verification reference;
- any other declared result required by the workflow.

Operational execution state MAY be tracked separately:

```text
queued → running → completed
                 ↘ failed
```

Do not confuse operational state with business output.

---

## 9. Function Organization

Code Stacker does not mandate one directory depth.

Small applications MAY place functions directly inside a workflow:

```text
onboard/
├── 01_profile/
├── 02_verify/
├── 03_payment/
├── 04_crawl/
├── 05_build/
├── 06_publish/
└── pipeline.py
```

Larger applications MAY group functions by domain:

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

The domain folder is organizational.

The function folder remains the implementation boundary.

> **Code Stacker is strict about boundaries and loose about folders.**

---

## 10. Workflows and Pipelines

A **workflow** composes bounded functions into useful application behavior.

A workflow may be:

- linear;
- branching;
- conditional;
- asynchronous;
- scheduled;
- event-driven;
- fan-out/fan-in;
- paused for human approval;
- resumed later.

Example:

```text
New Business
   ↓
create profile
   ↓
verify business
   ↓
check credits
   ↓
crawl website
   ↓
build package
   ↓
human edit
   ↓
publish
```

The workflow owns:

- execution order;
- branch conditions;
- retry policy;
- pauses;
- approvals;
- workflow state;
- mapping one function's output into another function's input.

A workflow MUST NOT duplicate the internal business behavior of its functions.

### 10.1 Pipeline implementation

A pipeline may be implemented as:

- direct in-process calls;
- a workflow engine;
- queue-backed jobs;
- database-driven state machine;
- event orchestration;
- durable execution runtime;
- a combination of these.

The implementation mechanism does not change the function contracts.

---

## 11. User Interface Model

Code Stacker maps naturally to user interfaces.

Users generally see sections, buttons, forms, links, dashboards, and workflow status.

```text
Dashboard
├── Verify Business
├── Crawl Website
├── Review Profile
├── Build Package
└── Publish
```

Those controls SHOULD start, resume, or act on workflows.

```text
UI action
    ↓
workflow
    ↓
bounded function(s)
    ↓
declared output + durable state
    ↓
UI renders state/result
```

A dashboard is therefore primarily:

- a workflow launcher;
- a workflow resume surface;
- a current-state view;
- an output/result view.

Business rules SHOULD remain in functions.

The frontend SHOULD NOT become a second workflow engine.

Not every internal function needs a dedicated UI control or public endpoint. Multiple functions MAY compose one user-facing workflow.

---

## 12. Integration Layer

Shared external connectivity SHOULD be explicit.

Recommended structure:

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
├── webhooks/
├── browser/
└── search/
```

These categories are examples, not requirements.

### 12.1 Integration responsibility

An integration owns connectivity details such as:

- authentication;
- SDK/client setup;
- transport;
- provider request/response shape;
- tool/command invocation;
- timeouts;
- rate limits;
- provider errors;
- provider-specific retries.

A function owns the product decision that uses that capability.

Example:

```text
functions/billing/check_credits
               ↓
integrations/api/stripe
```

The Stripe integration knows Stripe.

The billing function knows what the product means by "enough credits."

### 12.2 Shared integrations

Multiple functions MAY use one integration.

```text
functions/email/send ─────┐
functions/email/read ─────┼→ integrations/api/gmail
functions/email/search ───┘
```

This is acceptable reuse.

Functions MUST NOT use shared integrations as hidden channels to orchestrate each other.

### 12.3 Integration handoff

A non-trivial shared integration SHOULD contain:

```text
INTEGRATION.md
DESIGN.md
```

`INTEGRATION.md` defines the normalized capability exposed to functions.

`DESIGN.md` explains the implementation.

A substantial new integration SHOULD be built in its own fresh coding session before a function session depends on it.

---

## 13. API Integrations

API adapters MAY cover REST, GraphQL, SDKs, RPC, SaaS provider clients, and internal service APIs.

They SHOULD normalize provider-specific output before returning it to functions.

Provider raw payloads SHOULD NOT become cross-function contracts unless explicitly intended.

---

## 14. MCP Integrations

MCP is an integration transport.

A runtime function MAY use an MCP server through an explicit integration adapter.

The integration SHOULD declare:

- server;
- tool;
- input schema;
- normalized output;
- timeout;
- failure behavior.

A coding agent voluntarily choosing whether to use a development-time MCP tool MUST NOT be relied on as a correctness mechanism.

---

## 15. CLI Integrations

CLI tools MAY be wrapped as integrations.

Examples:

- Crawl4AI;
- ffmpeg;
- pandoc;
- git;
- image tooling;
- language-specific utilities.

The CLI integration SHOULD own:

- executable discovery/configuration;
- command construction;
- environment;
- timeout;
- stdout/stderr parsing;
- exit-code normalization.

Functions SHOULD consume normalized results rather than parse raw command output themselves.

---

## 16. Model Integrations

Model providers are integrations.

A function MAY invoke a model to perform its bounded responsibility.

The integration MAY own provider routing and transport, while the function owns:

- prompt/task intent;
- required structured output;
- validation;
- product-specific acceptance rules;
- persistence.

Model output MUST be validated before it becomes durable business state when correctness requires a structured contract.

---

## 17. Database

The database is usually the durable source of business truth.

Each durable record SHOULD have a clear owning function/domain.

Example:

```text
verify owns:
  business_verifications

crawl owns:
  crawl_runs
  crawl_evidence

build owns:
  package_artifacts

publish owns:
  publish_records
```

A downstream function MAY read upstream-owned state.

It SHOULD NOT silently rewrite upstream-owned business records.

Functions SHOULD pass stable IDs/references rather than duplicate large records through workflow state.

---

## 18. Redis / Cache

Redis or Valkey MAY be used for:

- cache;
- locks;
- rate limiting;
- queue backend;
- short-lived deduplication;
- leases;
- ephemeral coordination.

Redis SHOULD NOT silently become the only durable source of required business truth.

Keys SHOULD be namespaced and temporary keys SHOULD have TTLs.

---

## 19. Queues and Workers

Queues are execution transport.

They MUST NOT create a second business implementation.

Preferred:

```text
workflow
   ↓
queue
   ↓
worker
   ↓
same canonical function entry point
```

The worker and tests MUST exercise the same canonical function implementation used by production.

---

## 20. Object and File Storage

Large payloads SHOULD be stored and referenced.

Examples:

- crawl dumps;
- PDFs;
- media;
- generated sites;
- archives;
- model artifacts.

Output:

```yaml
artifact_id: pkg_123
artifact_uri: s3://bucket/pkg_123.zip
```

Do not push large binary payloads through workflow context unless required.

---

## 21. Platform Layer

Generic infrastructure MAY live in a platform layer:

```text
platform/
├── db/
├── redis/
├── queue/
├── auth/
├── telemetry/
├── config/
├── http/
└── storage/
```

Platform code SHOULD be generic.

If code answers a product-specific business question, it belongs in a function/domain rather than generic platform code.

---

## 22. AI Coding Session Standard

One function change SHOULD equal one fresh coding session.

The session reads:

1. `FUNCTION.md`;
2. `DESIGN.md`;
3. required upstream/downstream contract information;
4. shared platform interfaces;
5. integration contracts it consumes;
6. relevant schema.

The session MUST NOT:

- redesign unrelated workflows;
- modify sibling functions;
- fix unrelated issues;
- create duplicate runtime implementations;
- widen scope because it noticed another problem.

If a missing integration is required, the coder SHOULD report it or build it in a separately scoped integration session.

### 22.1 Session completion

Before ending, the coder SHOULD prove:

- canonical entry point accepts the declared input;
- declared output is produced;
- declared side effects occur;
- relevant failures are handled;
- tests exercise the canonical entry point;
- `FUNCTION.md` still reflects the contract;
- `DESIGN.md` reflects the code that actually exists.

Then the session ends.

The next bounded unit starts in a fresh session.

---

## 23. Production-Test Rule

This rule is mandatory:

> **The test, worker, and production path MUST execute the same canonical function.**

Bad:

```text
test → provider directly → success

production → create status row → provider never called
```

Good:

```text
test     ─┐
workflow ─┼→ function.run(input) → provider → persistence → output
worker   ─┘
```

Tests MAY substitute an external provider at the adapter boundary.

They SHOULD NOT bypass the business function being tested.

---

## 24. Error Model

Functions SHOULD expose stable errors for expected failure modes.

Errors and business output are distinct concepts.

Example:

```json
{
  "error": {
    "code": "CRAWL_SITE_UNREACHABLE",
    "message": "The website could not be reached.",
    "retryable": true
  }
}
```

A function does not need a binary `passed/failed` result field unless that result is part of its business contract.

---

## 25. Idempotency

Functions that create money movement, external resources, duplicate paid work, or published state SHOULD define idempotency behavior.

Examples:

- charge/debit credits;
- create crawl job;
- send email;
- publish package;
- create invoice;
- deploy artifact.

A retry SHOULD either return the prior durable result or safely repeat the operation when repetition is explicitly allowed.

---

## 26. Observability

It SHOULD be possible to answer:

> Where is this workflow, what ran, what did it return, and where did it stop?

without reverse-engineering source code.

Useful metadata includes:

- workflow/run ID;
- step/function ID;
- attempt;
- input references;
- output references;
- duration;
- integration request IDs;
- error code;
- current workflow state.

---

## 27. Stack Neutrality

Code Stacker MAY be implemented with:

- Python / FastAPI;
- TypeScript / Node;
- Go;
- Rust;
- Ruby;
- Java;
- .NET;
- mixed runtimes;
- serverless;
- monoliths;
- services;
- desktop;
- mobile;
- local-first applications.

The standard is about ownership and handoffs, not the framework.

Cross-runtime function execution MAY use:

- queues;
- local RPC;
- process calls;
- container jobs;
- durable execution;
- network services when justified.

Do not create a network API solely because two internal functions use different languages unless the operational design requires it.

---

## 28. Example: Business Publishing Workflow

```text
01_profile
INPUT: website URL + profile selection
OUTPUT: normalized business input
      ↓
02_verify
INPUT: normalized business
OUTPUT: verification record
      ↓
03_payment
INPUT: customer + product
OUTPUT: credit authorization ID
      ↓
04_crawl
INPUT: website URL + authorization
DO: Crawl4AI
OUTPUT: crawl run ID + evidence IDs
      ↓
05_review
INPUT: evidence IDs
OUTPUT: reviewed evidence
      ↓
06_build
INPUT: reviewed profile + prebuilt template
OUTPUT: package artifact ID
      ↓
07_publish
INPUT: approved artifact
OUTPUT: public URL + deployment ID
```

Notice:

- payment does not know Crawl4AI;
- crawl does not implement billing;
- build does not verify the business;
- publish does not recreate the package;
- outputs are typed business handoffs, not generic pass/fail flags.

---

## 29. Example: Email Domain

```text
functions/email/
├── search/
├── read/
├── send/
├── draft_reply/
└── summarize/
```

A workflow may compose them:

```text
User clicks "Prepare Reply"
      ↓
search email
      ↓
read thread
      ↓
summarize context
      ↓
draft reply
      ↓
pause for user approval
      ↓
send email
```

The UI sees one user workflow.

The codebase contains several independently owned functions.

The email provider integration may be shared by search, read, and send.

---

## 30. Compliance Checklist

A Code Stacker application should satisfy all of the following:

- [ ] business capabilities are divided into bounded functions;
- [ ] every function has a clear folder boundary;
- [ ] every function has `FUNCTION.md`;
- [ ] every function has `DESIGN.md`;
- [ ] every function declares input and output;
- [ ] business output is not forced into pass/fail;
- [ ] functions do not orchestrate sibling functions;
- [ ] workflows own composition and execution order;
- [ ] domain grouping is allowed without weakening function boundaries;
- [ ] reusable integrations are explicit;
- [ ] API, MCP, CLI, model, storage, and other transports remain integration concerns;
- [ ] product business rules remain in functions;
- [ ] UI actions invoke/resume workflows rather than duplicate workflow logic;
- [ ] durable data ownership is clear;
- [ ] workers call the canonical function entry point;
- [ ] tests call the canonical function entry point;
- [ ] large artifacts are passed by reference where practical;
- [ ] fresh coding sessions stay inside their assigned boundary;
- [ ] `DESIGN.md` is updated to match implementation before session completion.

---

## 31. Code Stacker in One Sentence

> **Build software like a team of focused engineers: one bounded function, one documented handoff, one fresh coding session at a time—then stack those functions into workflows that ship the product.**
