# AI Engineer Roles

Code Stacker can assign each fresh AI coding session a **specialized engineering role**.

The role defines scope: what the session owns, what it may inspect, what it must not change, and what handoff it must leave behind.

This is not about pretending the AI is a human employee. It is a practical way to keep coding agents focused.

## Function Engineer

Owns one bounded business capability.

Examples:

- crawl a website
- send an email
- verify a business
- generate an artifact
- publish a package

Owns:
- the function folder
- `FUNCTION.md`
- `DESIGN.md`
- function-local tests

Must not:
- redesign sibling functions
- change workflow order
- rebuild shared integrations

## Stripe Engineer

Owns the Stripe integration boundary.

Typical responsibilities:

- Stripe SDK/client setup
- checkout/payment/credit plumbing
- customer/payment/subscription lookups
- webhook normalization
- idempotency behavior
- provider errors
- normalized integration contract

Must not:
- decide product pricing policy
- decide whether a specific workflow is allowed to continue
- rewrite unrelated billing functions

## Auth Engineer

Owns authentication, session, identity, and authorization plumbing.

Typical responsibilities:

- auth provider integration
- login/session handling
- token validation
- user/org identity mapping
- role/permission plumbing
- auth middleware/interfaces

Must not:
- redesign product workflows
- move business policy into generic auth code

## API Engineer

Owns one reusable API integration boundary.

Typical responsibilities:

- SDK/HTTP client
- authentication
- request/response mapping
- retry/timeout behavior
- rate limits
- normalized errors
- integration documentation

Must not:
- embed product-specific business decisions into the API adapter

## MCP Engineer

Owns one MCP integration boundary.

Typical responsibilities:

- MCP server connection
- tool discovery/selection contract
- normalized tool inputs/outputs
- timeout/error handling
- authentication/configuration
- integration documentation

Must not:
- use MCP as hidden workflow orchestration
- redesign business functions

## Pipeline Engineer

Owns the **execution pipeline itself**.

Typical responsibilities:

- function order
- mapping one function's output into the next function's input
- async/sync execution
- retries
- pause/resume behavior
- user approval points
- pipeline state
- step status
- queue dispatch
- resume/recovery logic
- observability of where the run stopped

The Pipeline Engineer does **not** implement the business logic inside the functions.

Example:

```text
check_credits
  output: authorization_id
        ↓
pipeline maps authorization_id
        ↓
crawl_site
  input: authorization_id
```

The Pipeline Engineer owns the handoff.

The Billing Function Engineer owns what `check_credits` means.

The Crawl Function Engineer owns what `crawl_site` does.

### Pipeline Engineer rule

> **The pipeline coordinates functions. It does not become a giant function.**

The Pipeline Engineer must not:

- duplicate function business logic
- call providers directly when a function owns that provider behavior
- invent test-only state transitions
- bypass the canonical function entry point
- rewrite sibling functions to make the pipeline pass

## Workflow Engineer

Owns the user/business workflow definition.

A workflow is the product-level behavior the user experiences.

Example:

```text
User clicks "Publish Business"
      ↓
verify
      ↓
check credits
      ↓
crawl
      ↓
review
      ↓
build
      ↓
publish
```

The Workflow Engineer decides which existing capabilities belong in that user journey.

The Pipeline Engineer implements the reliable execution mechanics.

On small projects, one session may perform both roles if the boundary remains clear.

## UI Engineer

Owns the user-facing surface that starts, resumes, or displays workflows.

Typical responsibilities:

- pages
- forms
- buttons
- dashboard sections
- workflow status
- results
- error presentation

Must not:
- duplicate backend business rules
- invent a second workflow engine in the frontend

## Database Engineer

Owns shared schema/platform data work when the database change is broader than a single function.

Typical responsibilities:

- migrations
- schema relationships
- indexes
- shared constraints
- RLS/policy plumbing
- data migration

A function-specific table may still be owned by that Function Engineer.

## The general rule

> **Assign the smallest engineer role that can own the work completely.**

Do not hand one AI session five engineering jobs simply because the model can see the whole repository.
