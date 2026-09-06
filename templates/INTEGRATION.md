# Integration: <type/provider>

## Purpose

Describe the external capability this integration exposes to Code Stacker functions.

## Type

```yaml
type: api | mcp | cli | model | storage | webhook | browser | other
provider: <provider>
```

## Exposed Operations

List normalized operations that functions may use.

Example:

```text
create_payment(...)
retrieve_customer(...)
search_contacts(...)
run_crawl(...)
```

## Authentication

Describe how credentials are supplied.

Never place real secrets in this file.

## Input / Output

Document the normalized interface exposed to functions.

Do not force product functions to consume raw provider payloads unless that is intentional.

## Provider Mapping

Document which provider SDK/API/MCP tool/CLI command backs each normalized operation.

## Timeouts / Retries

Document timeout, retry, rate-limit, and idempotency behavior.

## Errors

Document normalized provider errors exposed to functions.

## Consumers

List the functions/workflows currently using this integration.

## DESIGN.md

Use `DESIGN.md` in this folder to explain how the current integration implementation actually works.
