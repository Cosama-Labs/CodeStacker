# Stack and Infrastructure Guide

Code Stacker is an application-architecture standard, not a framework. A function may use any technology required to perform its bounded responsibility, provided that infrastructure does not become a hidden orchestration layer.

## Database

The database is the durable source of business and workflow truth.

A function MAY read shared records required by its input contract and SHOULD be the single writer for records it owns. Cross-function writes should be avoided unless the write is explicitly part of the function contract.

Examples:

- PostgreSQL / Supabase Postgres
- MySQL
- SQLite for local/single-node applications
- SQL Server

The pipeline should pass IDs and small state, not entire database records.

```text
04_payment output: authorization_id
                         ↓
05_crawl input: authorization_id
```

The crawl function may validate that authorization from the database, but it must not implement payment policy.

## Redis / Valkey

Redis MAY be used for:

- cache
- locks
- rate limiting
- ephemeral coordination
- queue backend
- short-lived deduplication

Redis MUST NOT become the only durable representation of a business workflow state that must survive restart or eviction.

Preferred:

```text
Postgres: crawl status = running
Redis: worker lease = crawl:123
```

Avoid:

```text
Redis only: "somewhere in the crawl workflow"
```

## Queues and workers

Async execution does not change the function contract.

The synchronous pipeline and the queue worker should invoke the same canonical function entry point.

```text
pipeline
   ↓ enqueue function invocation
queue
   ↓
worker
   ↓
05_crawl.run(input)
```

The queue transport MAY be Celery, RQ, Dramatiq, BullMQ, SQS, RabbitMQ, Redis Streams, Kafka, or another implementation.

Queue technology is replaceable infrastructure. Business behavior remains in the function.

## External APIs

An external provider call belongs to the function whose business capability requires it.

Examples:

- verification → Google Places adapter
- payment → Stripe adapter
- crawl → Crawl4AI adapter
- notification → Resend/Twilio adapter

Provider adapters SHOULD normalize provider-specific inputs, outputs, timeouts, retries, and errors.

```text
05_crawl/
    function.py
    provider.py       # Crawl4AI adapter
    contract.py
```

The rest of the application should not need to understand the provider SDK.

## MCP

MCP is a runtime integration mechanism, not the pipeline.

A bounded function MAY call an MCP server if that is how the function obtains a capability or data source.

```text
07_enhance
   input
     ↓
   MCP client → business-data MCP server
     ↓
   normalized result
     ↓
   output
```

Rules:

- MCP tool selection MUST remain inside the owning function or adapter.
- A function MUST NOT use MCP to invoke sibling Code Stacker functions.
- The pipeline MUST NOT depend on a coding agent voluntarily calling an MCP server.
- Provider/MCP output SHOULD be normalized before becoming function output.

## AI / model calls

A model call is simply another external dependency.

```text
06_review
   input: crawl evidence IDs
      ↓
   load evidence
      ↓
   model call
      ↓
   validate structured result
      ↓
   persist review
      ↓
   output: review_id
```

A model provider MAY change without changing the pipeline contract.

Model output should be validated before it becomes durable application state.

## Object/file storage

Use object storage for large data that does not belong in the pipeline envelope.

Examples:

- crawl dumps
- PDFs
- media
- generated site packages
- archives
- large model artifacts

```text
08_build output:
  artifact_id: pkg_123
  artifact_uri: s3://bucket/pkg_123.zip
```

The next function receives the reference, not the entire artifact.

## Frontend

The frontend should interact with the application workflow or pipeline-facing API.

Preferred:

```text
React / mobile
      ↓
POST /onboard
      ↓
pipeline
```

Avoid creating a public HTTP API for every internal function unless the function is independently a product capability.

For workflows that pause for user action, the pipeline persists the current step and exposes the allowed action through the application API.

## Mixed stacks

Functions MAY use different languages or runtimes when necessary.

Example:

```text
01_profile     Python
02_verify      Python
03_detect      Go
04_payment     TypeScript
05_crawl       Python
06_review      Python
08_build       Node
10_publish     Go
```

The contract boundary remains the same. Cross-runtime transport may be a queue, process boundary, RPC, or durable job record, but sibling functions must not become tightly coupled through implementation details.

## Infrastructure rule

Infrastructure answers **how the function executes**.

The function contract answers **what the function does**.

Do not allow Redis, a queue, an API gateway, MCP, or a model provider to become the place where application workflow semantics are secretly implemented.
