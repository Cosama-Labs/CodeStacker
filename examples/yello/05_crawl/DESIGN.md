# Design: 05_crawl

## Canonical Entry Point

```text
run.py :: run(input, context)
```

The pipeline, worker, and tests must all execute this same function.

## File Map

```text
FUNCTION.md              business contract / handoff
DESIGN.md                current implementation map
contract.py              typed input/output
run.py                   canonical function implementation
repository.py            crawl/evidence persistence
providers/crawl4ai.py    Crawl4AI adapter
tests/                    canonical-entry tests
```

## Execution Flow

```text
declared input
    ↓
validate URL + authorization reference
    ↓
create/update crawl run
    ↓
Crawl4AI adapter receives website_url
    ↓
normalize returned pages
    ↓
persist evidence
    ↓
return crawl_run_id + evidence_ids + page_count
```

## Important Code

| Symbol | File | Responsibility |
|---|---|---|
| `run` | `run.py` | canonical production function |
| `Crawl4AIProvider` | `providers/crawl4ai.py` | provider transport/normalization |
| repository methods | `repository.py` | owned crawl/evidence persistence |

## Data Reads

- authorization record → confirm the pipeline-provided authorization reference is valid
- business identity → scope persisted crawl data

## Data Writes

- crawl run → execution and provider metadata
- crawl evidence/pages → normalized durable source evidence

## Redis / Queue

Queue transport may invoke this function asynchronously. Queue technology is not business logic and must not create a second crawl implementation.

## External APIs / MCP / Models

### Crawl4AI

- adapter: `providers/crawl4ai.py`
- operation: crawl the declared `website_url`
- result: normalized page/evidence data
- provider exceptions are normalized into function/workflow errors

No MCP or model call is required for this function.

## Data Transformations

```text
Crawl4AI provider result
      ↓
normalized page records
      ↓
database evidence rows
      ↓
IDs/counts returned as the business output
```

## Invariants

- payment/credit charging does not occur here
- the exact declared URL reaches the Crawl4AI adapter
- persisted evidence belongs to the created crawl run
- the function does not call the next workflow function
- tests do not call Crawl4AI directly as a substitute for testing `run`

## Error and Edge Handling

Expected failures include invalid URL, unreachable site, timeout, blocked crawl, provider error, or no usable content.

The runtime may mark the step failed, but failure state is separate from the successful business result contract.

## Tests

Tests should prove:

1. the canonical entry point sends the declared URL to the provider adapter;
2. returned pages are persisted;
3. output references the persisted crawl/evidence;
4. provider failure becomes the declared error behavior;
5. the worker/pipeline does not use another implementation path.

## UI / Workflow Relationship

A dashboard action such as **Crawl Website** starts/resumes the onboarding workflow. The pipeline reaches `05_crawl`, then returns progress/result state for the UI to render.

The UI does not call provider code or duplicate crawl business logic.

## Change Map

- provider request/response → `providers/crawl4ai.py`
- persisted evidence structure → `repository.py`
- input/output shape → `contract.py` and `FUNCTION.md`
- internal execution → `run.py`
- pipeline order → not in this folder

## Decisions and Why

Provider behavior is isolated so Crawl4AI can change without changing downstream contracts.

The output contains evidence references rather than the full crawl payload so the pipeline stays small and stable.

## Known Limitations

This is an architecture example. Actual provider options, schema names, and retry policy belong to the implementation using this template.

## Session Handoff Notes

A future coding session must update this file whenever the implementation structure changes.
