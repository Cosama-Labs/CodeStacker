# Function: 05_crawl

## Purpose

Crawl the authorized business website and persist usable source evidence for downstream review/build steps.

## How to Use

Canonical entry point:

```text
05_crawl.run(input, context)
```

The pipeline invokes this function after payment/credit authorization has already completed.

## Input

```yaml
business_id: uuid
website_url: url
authorization_id: uuid
crawl_profile: string
```

## Output

```yaml
crawl_run_id: uuid
requested_url: url
final_url: url | null
page_count: integer
evidence_ids: uuid[]
```

The business output is the persisted crawl/evidence reference. Operational execution status is tracked separately by the workflow runtime.

## Allowed Side Effects

- create/update the crawl-run record
- persist normalized crawl pages/evidence
- write crawl logs/metrics owned by this function
- optionally store large raw payloads in object storage

## External Dependencies

- Crawl4AI
- PostgreSQL
- optional object storage

## Example

### Input

```yaml
business_id: "biz_123"
website_url: "https://example.com"
authorization_id: "auth_456"
crawl_profile: "business-site"
```

### Output

```yaml
crawl_run_id: "crawl_789"
requested_url: "https://example.com"
final_url: "https://www.example.com/"
page_count: 12
evidence_ids:
  - "ev_001"
  - "ev_002"
```

## Failure / Edge Behavior

- invalid URL
- website unreachable
- provider timeout/error
- blocked site
- no usable content

Failures are represented by the workflow/function error contract; they are not the business output shape.

## Data Ownership

Reads:
- business identity needed to scope the crawl
- authorization record by `authorization_id`

Writes:
- website crawl run
- website crawl page/evidence records
- crawl status/log data

## UI / Workflow Surface

- invoked by the onboarding workflow after authorization
- may be initiated by a user-facing "Crawl Website" action
- progress/status may be displayed on a dashboard
- no direct public function API is required

## Forbidden

- MUST NOT charge credits
- MUST NOT populate the AI profile
- MUST NOT build a hosting package
- MUST NOT publish
- MUST NOT call `06_review` directly

## Handoff

See `DESIGN.md` for the implementation map.
