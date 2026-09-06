# Function: <NN_name>

## Purpose

One sentence describing the single business capability this function owns.

## How to Use

Canonical entry point:

```text
<run(input, context) -> output>
```

Describe when the pipeline/workflow invokes this function.

## Input

```yaml
field_name: type
```

## Output

Declare the business result this function returns.

The output does **not** have to be pass/fail.

```yaml
field_name: type
```

Examples of valid outputs include records, IDs, artifacts, scores, classifications, lists, URLs, approval requirements, retry information, or a durable-write verification reference.

## Allowed Side Effects

- durable write owned by this function
- event/job/artifact creation owned by this function

## External Dependencies

- provider / API / model / MCP / queue / storage dependency

## Example

### Input

```yaml
# realistic example
```

### Output

```yaml
# realistic expected result
```

## Failure / Edge Behavior

Document only the failure and edge cases relevant to this function.

## Data Ownership

Reads:
- tables/resources this function may read

Writes:
- tables/resources this function owns

## UI / Workflow Surface

- workflow(s) that invoke this function
- user action/button that may lead to it, if any
- whether the function is internal-only

## Forbidden

- MUST NOT call sibling functions
- MUST NOT redesign pipeline order
- MUST NOT modify unrelated folders
- MUST NOT move this function's business logic into pipeline code

## Handoff

See `DESIGN.md` for how the current implementation actually works.
