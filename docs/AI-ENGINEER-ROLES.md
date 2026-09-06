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

The Pipeline Engineer is the **lead engineer for a workflow**.

It understands the workflow as a whole, decides which bounded function or specialist is needed next, and hands that work to a fresh AI engineering session with a precise assignment.

Typical responsibilities:

- understand the user/business workflow end to end;
- define the ordered or conditional function map;
- decide which function should be built next;
- create or update that function's `FUNCTION.md`;
- tell the Function Engineer the rules, requirements, allowed dependencies, side effects, and expected output;
- identify when a specialist is needed instead, such as Stripe, Auth, API, MCP, Database, UI, or Verification Engineer;
- review the completed handoff;
- map the completed function's output into the next function's input;
- own retries, pauses, resume/recovery, pipeline state, and workflow observability;
- reject work that does not satisfy the declared function contract.

The Pipeline Engineer is part **technical lead**, part **integration owner**, and part **traffic controller**.

Example:

```text
Pipeline Engineer
      ↓
"This workflow now needs 05_crawl."
      ↓
creates/updates 05_crawl/FUNCTION.md
      ↓
defines:
  purpose
  input
  requirements
  allowed dependencies
  side effects
  expected output
  forbidden scope
      ↓
fresh Function Engineer session
      ↓
implements 05_crawl only
      ↓
returns working function + DESIGN.md
      ↓
Pipeline Engineer validates handoff
      ↓
connects crawl output → next function input
```

### What the Pipeline Engineer tells the Function Engineer

The assignment should answer:

- **What are you building?**
- **Why does this function exist?**
- **What input will you receive?**
- **What must the function do?**
- **What output must it produce?**
- **What may it read/write/create?**
- **Which integrations/platform capabilities may it use?**
- **What must it not touch?**
- **What proves completion?**

It should **not** prescribe low-level implementation unless a real architectural constraint requires it.

The Pipeline Engineer owns the workflow and the handoff. The Function Engineer owns the implementation inside the function boundary.

### Pipeline Engineer rule

> **Direct the engineers. Own the workflow. Do not become the whole team.**

The Pipeline Engineer must not:

- implement every function in one long session;
- absorb specialist work that deserves its own bounded session;
- move business logic into pipeline code;
- call providers directly when a function or integration owns that behavior;
- manufacture success by changing workflow state;
- weaken requirements because implementation is inconvenient.
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
