# Start Here — Code Stacker for Pure Vibe Coders

You do **not** need to be a software engineer to use Code Stacker.

You need to be able to explain:

- what the product should do;
- what the user gives it;
- what result should come back;
- what external services are involved;
- what should happen next.

The coding agent should own the implementation details inside those boundaries.

## The founder / vibe-coder job

You own the **WHAT**:

- product intent;
- workflow;
- business rules;
- priorities;
- inputs;
- outputs;
- constraints;
- acceptance criteria.

The AI coder owns the **HOW**:

- implementation structure inside the assigned function;
- classes/functions;
- provider calls;
- persistence;
- error handling;
- tests;
- debugging;
- keeping `DESIGN.md` current.

If you find yourself specifying every method, import, queue call, or internal object just to make the AI finish the task, the boundary is probably wrong.

---

## Step 1 — Start from the UI or user action

Write down what the user can do.

Example:

```text
Dashboard
├── Add Business
├── Verify Business
├── Crawl Website
├── Review Profile
├── Build Package
└── Publish
```

Those buttons are usually workflows or workflow actions.

Do not start by inventing backend classes.

---

## Step 2 — Break the workflow into bounded functions

Example:

```text
Add Business
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
publish
```

A function should be small enough that one fresh coding session can own it.

A function can still contain several files internally.

---

## Step 3 — Give every function a handoff contract

Create:

```text
FUNCTION.md
```

Write the function in plain English:

- what it does;
- how it is used;
- input;
- output;
- side effects;
- dependencies;
- example input/output;
- important edge cases;
- what it must not do.

You do **not** need to tell the AI how many classes to create or which helper function names to use.

Use [templates/FUNCTION.md](templates/FUNCTION.md).

---

## Step 4 — Separate shared integrations when useful

If several functions need the same provider, create an integration boundary.

Example:

```text
integrations/
├── api/
│   ├── stripe/
│   └── gmail/
├── mcp/
├── cli/
└── models/
```

The integration knows the provider.

The function knows the business rule.

Example:

```text
Stripe integration:
  "Here is the customer's credit balance."

Billing function:
  "This workflow requires 3 credits. Is the customer allowed to continue?"
```

---

## Step 5 — Open one fresh coding session for one function

Give the coder:

- the function folder;
- `FUNCTION.md`;
- `DESIGN.md` if the function already exists;
- any integration contract it needs;
- the relevant DB schema or platform interface.

Then use [skills/02-build-function/SKILL.md](skills/02-build-function/SKILL.md).

Do not ask the same session to keep building the next feature.

Finish the function. End the session.

---

## Step 6 — Prove the real function path

A passing provider script is not the same as a working feature.

Bad:

```text
test → Crawl4AI → success

real app → create DB row → never calls Crawl4AI
```

Good:

```text
test     ─┐
workflow ─┼→ same function.run(input) → Crawl4AI → DB → output
worker   ─┘
```

Use [skills/03-prove-runtime/SKILL.md](skills/03-prove-runtime/SKILL.md).

---

## Step 7 — Make the coder leave a map for the next coder

Before the session ends, the coder updates:

```text
DESIGN.md
```

It explains the code that actually exists:

- entry point;
- file map;
- execution flow;
- DB writes;
- integration calls;
- important symbols;
- tests;
- where future changes belong.

That replaces the need for the next AI session to inherit the previous chat.

Use [skills/04-write-handoff/SKILL.md](skills/04-write-handoff/SKILL.md).

---

## Step 8 — Compose the functions into workflows

The workflow connects declared outputs to declared inputs.

It owns:

- order;
- branching;
- retries;
- pauses;
- approvals;
- state.

It should not reimplement the functions.

Use [skills/06-compose-workflow/SKILL.md](skills/06-compose-workflow/SKILL.md).

---

## A tiny example

```text
User clicks "Publish Business"
        ↓
workflow starts
        ↓
check_credits
  output: authorization_id
        ↓
crawl_site
  output: crawl_run_id + evidence_ids
        ↓
build_package
  output: artifact_id
        ↓
publish
  output: public_url
        ↓
dashboard shows result
```

That is the whole idea.

## The goal

Code Stacker does not make AI coding perfect.

It tries to make failure **local, visible, replaceable, and understandable** instead of letting one long coding session damage the whole application.

> **You describe the product. The AI engineers one bounded piece. The folder preserves the handoff. The workflow stacks the pieces together.**
