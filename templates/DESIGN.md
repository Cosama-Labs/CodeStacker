# Design: <NN_name>

> This file describes **how the current function is implemented**.
> `FUNCTION.md` defines what the function promises. `DESIGN.md` explains how the code fulfills that promise.

## Canonical Entry Point

```text
<file> :: <function/method>
```

This MUST be the entry point used by production execution and exercised by function tests.

## File Map

```text
run.py          - orchestration inside this function boundary
contract.py     - input/output types
repository.py   - persistence owned by this function
providers/      - external provider adapters
tests/          - tests of the canonical entry point
```

Describe only files that actually exist.

## Execution Flow

```text
input
  ↓
validate
  ↓
load required state
  ↓
perform business operation
  ↓
persist owned state
  ↓
produce declared output
```

Replace this with the real implementation flow.

## Important Code

Document important classes/functions and what each one owns.

| Symbol | File | Responsibility |
|---|---|---|
| `run` | `run.py` | canonical function entry |
| ... | ... | ... |

## Data Reads

- table/resource → why it is read

## Data Writes

- table/resource → what this function owns and writes

## Redis / Queue

Document keys, queue names, jobs, locks, TTLs, and why they exist.

If unused, write: **None.**

## External APIs / MCP / Models

For each dependency, document:

- provider/server/model
- adapter file
- operation/tool called
- normalized result used by the function
- timeout/retry behavior

If unused, write: **None.**

## Data Transformations

Describe important transformations between input, provider data, persisted state, and output.

## Invariants

Facts that must remain true for this implementation to be correct.

Examples:

- payment is not performed inside this function
- a completed crawl references persisted evidence
- the canonical entry point is the only production implementation

## Error and Edge Handling

Describe how the implementation handles expected failures and unusual inputs.

## Tests

List the tests that exercise the canonical entry point and what each proves.

Do not describe a provider-only test as proof that the production function works.

## UI / Workflow Relationship

Document:

- pipeline/workflow step that invokes this function
- user-facing screen/button that leads to it, if any
- output/state surfaced back to the UI
- whether the function has no direct user-facing surface

## Change Map

For common future changes, show where a future coder should work.

Example:

- change provider request → `providers/provider.py`
- change persisted crawl fields → `repository.py` + contract
- change function result shape → `contract.py` + `FUNCTION.md`
- change pipeline order → **not in this folder**

## Decisions and Why

Record non-obvious implementation decisions that a future session should not have to rediscover.

## Known Limitations

List real known limitations only.

## Session Handoff Notes

Before ending a coding session, update this section with anything a future fresh session needs to know.

**Rule:** `DESIGN.md` MUST describe the code that actually exists, not the design the coder intended to build.
