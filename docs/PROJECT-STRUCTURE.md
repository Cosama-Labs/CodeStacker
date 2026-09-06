# Project Structure

Code Stacker does **not** require one rigid application tree.

The standard defines boundaries and handoffs. Teams may organize those boundaries in the way that best matches the product, stack, and deployment model.

A useful larger-project structure is:

```text
app/
├── functions/
│   ├── email/
│   │   ├── send/
│   │   │   ├── FUNCTION.md
│   │   │   ├── DESIGN.md
│   │   │   └── ...
│   │   ├── read/
│   │   ├── search/
│   │   └── summarize/
│   ├── customers/
│   │   ├── create/
│   │   ├── verify/
│   │   └── enrich/
│   ├── billing/
│   │   ├── check_credits/
│   │   └── debit_credits/
│   └── publishing/
│       ├── build/
│       └── publish/
│
├── workflows/
│   ├── onboarding/
│   │   ├── pipeline.yaml
│   │   └── DESIGN.md
│   ├── email_assistant/
│   ├── publish_business/
│   └── billing/
│
├── integrations/
│   ├── api/
│   │   ├── stripe/
│   │   ├── google_places/
│   │   └── resend/
│   ├── mcp/
│   │   ├── twenty/
│   │   └── github/
│   ├── cli/
│   │   └── crawl4ai/
│   ├── models/
│   ├── storage/
│   └── webhooks/
│
├── platform/
│   ├── db/
│   ├── redis/
│   ├── queue/
│   ├── auth/
│   ├── telemetry/
│   └── config/
│
└── ui/
    └── <framework-specific structure>
```

## Functions

A **function** is a bounded business capability.

Examples:

- send an email
- search email
- verify a business
- check credits
- crawl a website
- generate a package
- publish an artifact

Functions may be grouped by domain for human clarity.

The important boundary is the function folder itself.

Every function folder should contain:

- `FUNCTION.md` — what the function promises
- `DESIGN.md` — how the current implementation works
- canonical implementation code
- function-local tests

A function may be used by one workflow or many workflows.

## Workflows

A workflow composes functions into user-visible or system-visible behavior.

Examples:

```text
New Business
  → create customer
  → verify business
  → check credits
  → crawl
  → build
  → publish
```

```text
Email Assistant
  → search email
  → read selected thread
  → summarize
  → draft reply
```

A workflow owns:

- sequence
- branching
- retries
- pauses
- user approvals
- current workflow state

A workflow does **not** own the implementation details of the functions it invokes.

## Integrations

Integrations are reusable connectivity boundaries.

They answer questions like:

- how do we call Stripe?
- how do we call a specific MCP server?
- how do we invoke a CLI tool?
- how do we call a model provider?
- how do we store an object?
- how do we process a webhook?

Suggested organization:

### API

```text
integrations/api/
  stripe/
  google_places/
  resend/
```

### MCP

```text
integrations/mcp/
  twenty/
  github/
  slack/
```

### CLI

```text
integrations/cli/
  crawl4ai/
  ffmpeg/
  pandoc/
```

### Other useful integration groups

```text
integrations/
  models/
  storage/
  webhooks/
  browser/
  search/
  payments/
```

The exact categories are optional.

Use categories that make the system easier to navigate.

### Functions own business behavior; integrations own connectivity

Example:

```text
functions/billing/check_credits
            ↓
integrations/api/stripe
```

The Stripe integration knows Stripe's SDK, authentication, request shapes, timeouts, and provider errors.

The billing function knows what **enough credits** means for the product.

Do not put product business rules into the Stripe integration.

## Shared integrations are allowed

The same integration may support many functions.

For example:

```text
functions/email/send ─────┐
functions/email/search ───┼→ integrations/api/gmail
functions/email/read ─────┘
```

This is not forbidden coupling.

The functions are depending on a declared platform capability rather than importing each other's internals.

## Integration handoff

A non-trivial shared integration should document its contract.

Recommended:

```text
integrations/api/stripe/
├── INTEGRATION.md
├── DESIGN.md
└── ...
```

`INTEGRATION.md` describes:

- capability exposed
- authentication
- normalized operations
- inputs/outputs
- provider-specific limits
- error behavior

`DESIGN.md` describes the actual implementation.

An integration may therefore be assigned its own fresh coding session, just like a function, when it needs substantial implementation work.

## Platform

The platform layer contains generic infrastructure shared by the application.

Examples:

- DB connections
- Redis clients
- queue transport
- authentication context
- configuration
- telemetry
- generic HTTP transport

Platform code should not answer product-specific business questions.

## UI

Code Stacker does not prescribe a React, Next.js, Vite, mobile, or desktop folder structure.

Instead it defines the relationship between the UI and workflows.

```text
Dashboard / Page
      ↓
button, link, form, action
      ↓
workflow
      ↓
bounded function(s)
      ↓
durable state + output
      ↓
UI renders result/status
```

A dashboard is often just a collection of:

- workflow launchers
- workflow resume actions
- current state
- outputs
- links into user-facing capabilities

Example:

```text
Business Dashboard
├── Verify Business
├── Crawl Website
├── Review Profile
├── Build Package
└── Publish
```

Those controls do not each need to know how the backend feature works.

They initiate or continue the relevant workflow.

## Small applications

A small app does not need all of these top-level folders.

This is valid:

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

Each function can keep its provider adapter locally.

## Larger applications

As reuse grows, extract common integrations and organize functions by domain.

The architecture can evolve from:

```text
workflow/function/provider.py
```

to:

```text
functions/domain/function/
integrations/type/provider/
workflows/name/
```

without changing the Code Stacker principles.

## The rule

> **Code Stacker is strict about boundaries and loose about folders.**

Organize the repository in whatever way makes the product understandable, provided that:

1. functions remain bounded;
2. functions remain self-describing;
3. integrations remain explicit;
4. workflows own orchestration;
5. the UI invokes workflows rather than reimplementing them;
6. fresh coding sessions can understand their assigned boundary without reconstructing the whole application.
