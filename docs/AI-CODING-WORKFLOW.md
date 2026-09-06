# AI Coding Workflow

Code Stacker is designed so an AI coding agent can work like one focused engineer on a larger software team.

The application architecture persists across sessions. The conversation does not need to.

## Before a session

The product owner/team defines the function-level behavior:

- purpose
- input
- output
- allowed side effects
- external dependencies
- workflow position
- data ownership
- user-facing capability, if any

The human does not need to pre-design every internal class, function signature, queue primitive, or provider call.

## Every function folder has two handoff documents

### `FUNCTION.md`

Defines **what the function promises**:

- what it does
- how it is invoked
- input
- output
- side effects
- dependencies
- example use
- edge/failure behavior
- data ownership
- workflow/UI relationship

### `DESIGN.md`

Defines **how the current code actually fulfills that promise**:

- canonical entry point
- file map
- execution flow
- important classes/functions
- DB/Redis/queue behavior
- API/MCP/model calls
- data transformations
- invariants
- tests
- implementation decisions
- change map
- handoff notes

A fresh session reads both.

`FUNCTION.md` is the contract.  
`DESIGN.md` is the implementation map.

## Session scope

One coding session owns one function folder.

The agent may inspect:

- `FUNCTION.md`
- `DESIGN.md`
- the upstream output contract
- shared platform interfaces required by the function
- provider documentation
- database schema relevant to the function
- pipeline metadata needed to understand invocation

The agent must not modify sibling function folders or redesign pipeline order.

## Recommended session instruction

```text
You are the engineer assigned to exactly one Code Stacker function.

Read FUNCTION.md first to understand the contract.
Read DESIGN.md second to understand the current implementation.

Work only inside this function folder except for explicitly approved shared
platform interfaces.

Do not modify sibling function folders.
Do not redesign pipeline order.
Do not move business logic into pipeline code.
Do not invent a second production implementation.

The canonical function entry point used by production must also be the entry
point exercised by its tests.

The declared output is a business result and does not need to be pass/fail.

If the function contract cannot be implemented with available platform
interfaces, report the missing dependency rather than changing another function.

Before finishing, update DESIGN.md so it describes the code that actually exists.
```

## Completion

A function session is complete when:

1. the canonical entry point accepts the declared input;
2. it produces the declared business output;
3. declared side effects are implemented;
4. relevant edge/failure behavior is represented;
5. tests exercise the same canonical entry point used by production;
6. no sibling function was modified to manufacture success;
7. `FUNCTION.md` still matches the public contract;
8. `DESIGN.md` matches the implementation that actually exists.

Then end the session.

Start the next function in a fresh session.

## Why fresh sessions

The session boundary prevents assumptions, shortcuts, stale context, and unrelated implementation history from leaking across capabilities.

The next coder does not need the prior conversation.

The handoff lives in the folder.

> **The code is not the handoff. The folder is the handoff.**

## UI and workflow work

User interfaces follow the same ownership model.

A UI section or dashboard button represents a user-facing capability and typically starts, resumes, or acts on a workflow.

```text
button / link
    ↓
workflow / pipeline
    ↓
bounded function(s)
    ↓
durable state + declared output
    ↓
UI result/status
```

A UI coding session should know the workflow contract it invokes, but it should not duplicate backend business rules.

Not every function requires a direct screen. Multiple bounded functions may compose one user workflow.

## Change work

When a function changes later:

1. open a fresh session scoped to that function;
2. read `FUNCTION.md`;
3. read `DESIGN.md`;
4. change implementation only inside the boundary;
5. change the contract only if product behavior intentionally changed;
6. update `DESIGN.md` before closing the session.

A future coder should never have to reconstruct the function from old AI chat history.
